---
layout: post
title: "Ajax Request in Ruby On Rails (UJS)"
date: 2012-10-10 06:09:19 +0530
comments: true
categories: [rails, ajax, ujx]
---

Many programmer uses jquery to catch click event and do $.get or $.post to access the server and print the response in a required div.

But there is a handy way to do ajax request using UJS provided by Rails .

Let us take an example of `add to wish list` event.

This can be easily achieved in three easy steps.
Now lets see how to do that in rails using UJS

#### Step 1:

In view(`show.haml.html`) your may have

```ruby  
= link_to "Add to wishlist" , wish_list_path(@product) ,:id => "wish_list_link"
```

Now we make it as ajax request by adding `:remote => true`

```ruby  
= link_to "Add to wishlist" , wish_list_path(@product) ,:id => "wish_list_link" ,:remote => true
```

#### Step 2:
Now in controller

```ruby products_controller.rb
def wish_list
  #adding wish list 
  respond_to do |format|
     format.js #This line is important
   end
end 
```    

#### Step 3:
Now create a file named wish_list.js.erb

```erb wish_list.js.erb
$("#wish_list_link").html(''<b>added to wishlist</b>");

# Lets say you want to render a partial .

$("#wish_list_linkt").html('<%= escape_javascript(render :partial => "wish_list" , :locals => { :product => @product})%>');

```

#### Step 4(optional):
You can use jquery to show the loading part while server is responding

```ruby 
$("#wish_list_link").live("click",function(){
    #show_loading_img
})
```

That's it. As simple as that.