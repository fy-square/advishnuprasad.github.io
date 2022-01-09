---
layout: post
title: "Generate API documentation using rspec acceptance tests"
date: 2016-02-07 11:43:58 +0530
comments: true
categories: [rails, apidocs, rspec]
---

#### Step 1:

Install required gems

```ruby
gem 'rspec_api_documentation'
gem 'raddocs'
```

#### Step 2:

Create acceptance_helper.rb in spec folder

```ruby acceptance_helper.rb
require 'rails_helper'
require 'rspec_api_documentation'
require 'rspec_api_documentation/dsl'

RspecApiDocumentation.configure do |config|
  config.format = :json
  config.curl_host = 'http://fysquare.dev' # Will be used in curl request
  config.api_name = "FySquare API" # Your API name
  config.request_headers_to_include = ["Host", "Content-Type"]
  config.response_headers_to_include = ["Host", "Content-Type"]
  config.curl_headers_to_filter = ["Authorization"] # Remove this if you want to show Auth headers in request
  config.keep_source_order = true
end

```

Create raddoc.rb in initializers

```ruby raddoc.rb
Raddocs.configure do |config|
  config.api_name = "FySquare API Documentation"
end
```

Add API route in config.rb

```ruby routes.rb
mount Raddocs::App => "/api_docs"
end
```

#### Step 3:

Now you need to write acceptance specs which will be used to generate docs.

Below is the spec for listing users API. You can change the params depends on your API.

```ruby users_spec.rb
require 'acceptance_helper'

resource "User", acceptance: true do
  let(:user) { FactoryGirl.create(:user) }
  before do
    header 'Authorization', 'Auth code'
    header 'Content-Type', 'application/json'
  end

  get "/api/users" do
    example_request "Listing Users" do
      explanation "List all the users in the system"
      expect(status).to eq 200
    end

    parameter :ids, "List of user ids to be fetched"
    parameter :email, "Search users by email"
    parameter :page, "Page to view"
    example "Get users by params" do
      explanation "Get users by params"

      do_request(:ids => [user.id])
      expect(status).to eq 200

      do_request(:email => ['foo@example.com', 'doo@example.com'])
      expect(status).to eq 200

    end
  end

  get "/api/users/:id" do
    let(:id) { user.id }
    example_request "Getting a specific user" do
      expect(status).to eq(200)
    end
  end
end
```

#### Step 4:

Run this rake command to generate API.

```ruby
rake docs:generate
```

Now navigate to `localhost:3000/docs`

![API docs image]({{ site.url }}/images/api_doc1.png)
