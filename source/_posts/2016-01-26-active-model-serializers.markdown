---
layout: post
title: "Active Model Serializers"
date: 2014-11-14 19:42:22 +0530
comments: true
categories: [rails, json, serializers]
---

### When to use active model serializer ?

When you have your app running on both browser and mobile app, and if you have a backend rails server then active model serialiser is a perfect gem for serving APIs.

### Gems

There are other gems like rabl but I personally prefer `active_model_serializers` gem because this is extremely easy to use and it has lot of + points.

#### Step 1 : Add it to your Gemfile

```ruby
gem 'active_model_serializers', '~> 0.9.3'
```

#### Step 2 : Create a serializer

Create a serializer file by running
```ruby
rails g serializer user
```
which will create `app/serializers/user_serializer.rb`



#### Step 3 :

In serializer, you can add attributes, associations, custom methods.

```ruby user_serializer.rb
class UserSerializer < ActiveModel::Serializer
  attributes :id, :name, :followers, :timestamp
end
```

So here id and name are user fields. Followers is an association in user. timestamp is a custom method.

You can define that method like this.

```ruby
def timestamp
  object.created_at.strftime(“%B %d %Y”)
end       
```

#### Step 4 :

In your controller, make sure you are rendering json and html
```ruby
respond_with @user
```

When you visit `/users/1.json`

```json   
{
    "user": {
        "id": 1,
        "email": "foodoo@example.com",
        "name": "Foo doo",
        "microposts": [
            {
                "id": 1,
                "content": "This is a micropost",
                "user_id": 1,
                "created_at": "2014-11-11T16:11:08.500Z",
                "updated_at": "2014-11-11T16:11:08.500Z"
            }
        ],
        "followers": [ ]
    }
}
```