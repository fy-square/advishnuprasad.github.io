---
layout: post
title: "SOAP Request from Ruby without Savon"
date: 2016-03-25 21:11:47 +0530
comments: true
categories: [ruby, soap]
---

### Send SOAP request from ruby net/http without using Savon gem

You would have seen everyone is using Savon gem for sending SOAP request. But what if the service is using SOAP 1.1.
You might want to write your own http request. Here is the way to send request with SOAP envelope body.

```ruby
require 'net/https'
require 'uri'
require 'nokogiri'

# Create the http object
url = "http://www.webservicex.net/globalweather.asmx?WSDL"
uri = URI.parse(url)
http = Net::HTTP.new(uri.host, uri.port)
http.use_ssl = false

# Raw SOAP XML to be passed
# TODO : Date should be dynamic
data = <<-EOF
<soap:Envelope xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
  <soap:Body>
    <GetWeather xmlns="http://www.webserviceX.NET">
      <CityName>Madras</CityName>
      <CountryName>India</CountryName>
    </GetWeather>
  </soap:Body>
</soap:Envelope>
EOF

# # Set Headers
# SOAPAction is mandatory
headers = {
  'Content-Type' => 'text/xml; charset=utf-8',
  'SOAPAction' => "http://www.webserviceX.NET/GetWeather"
}

result= http.post(uri.path, data, headers)

# Parse
doc = Nokogiri::XML(result.body)
doc.remove_namespaces! # Remove namespaces from xml to make it clean
p doc
```
