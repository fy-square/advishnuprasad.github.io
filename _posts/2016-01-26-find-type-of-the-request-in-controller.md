---
layout: post
title: "Find type of the request in controller"
date: 2012-12-15 07:51:00 +0530
comments: true
categories: [rails, tips]
---

Is there any way to find the type of request in controller ?

#### Yes.

What we do in `routes.rb` is , define the type of request to particular action.

But, what If we want to find out the type of request in controller .

```ruby
if request.get?
   logger.info "Request is GET"
elsif request.post?
   logger.info "Request is POST"
elsif request.xhr?
   logger.info "Request is AJAX"
end
```

