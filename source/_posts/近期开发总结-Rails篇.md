---
title: 近期开发总结-Rails篇
date: 2016-08-22 22:06:26
tags: 开发总结
categories: Rails
---

# Rails problems summary during developing <!--more-->

- How to add default value to a text field in rails?
    + `<p><%= f.number_field :product_id, :value => @product.id %></p>`
    + `<%= f.hidden_field :product_id %>`
    
- Notice the `find` and `where` difference
    + the  `find` return the first object
    + the `where` return the all objects 

- When i realize the 'like' action, I must add two routes 'get' and 'post' for the button,i don't why ,but it's work... 

- How to pass the variable to partical views
    +  `<%= render partial: "like_app", locals: {like_app: like_app} %>`

- Error: Couldn't find User with id=search
    + [The solutions](http://stackoverflow.
