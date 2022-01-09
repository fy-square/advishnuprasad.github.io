---
layout: post
title: "Fetch records from sql query - Ruby On Rails - Oci8 cursor"
date: 2014-03-14 18:50:26 +0530
comments: true
categories: [rails, oracle]
---

There might be time when you can't use ActiveRecord finders but have to write custom 
queries to fetch the records.

Below example is to fetch records using oci8 adapter.(i.e If you are using oracle db)

```ruby
users = ActiveRecord::Base.connection.execute("select id, email from users" )
```

The output will be
  
```ruby  
<OCI8::Cursor:0xf73ac30>
```

which is nothing but a cursor object. 
But how to read the records from this cursor?.

```ruby
while r = users.fetch_hash
   puts users
end
```

The output will be

```json  
{"ID"=>10005, "EMAIL"=>"2@gmail.com"}
{"ID"=>10006, "EMAIL"=>"3@gmail.com"}
{"ID"=>10002, "EMAIL"=>"1@gmail.com"}

```