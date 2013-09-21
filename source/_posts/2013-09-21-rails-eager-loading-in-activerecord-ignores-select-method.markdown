---
layout: post
title: "RAILS: Eager Loading in activerecord ignores select method"
date: 2013-09-21 03:25
comments: true
categories: [rails, activerecord]
---

If your trying to use the select method in Activercord, and you are finding that your select does not work, your issue may be with eager loading. It turns out that when you eager load with the includes method, the select method is completely ignored. 

<!-- more -->

## This does not work

{% codeblock example_controller.rb %}
 @posts = Post.select('title, categories.name AS category_name')
              .joins(:category)
              .includes(:category)
              .where("categories.name='News'")
 @grouped_posts = @posts.group_by(:category_name)
{% endcodeblock %}

## Solution 

Remove the includes.

{% codeblock example_controller.rb %}
  @posts = Post.select('title, categories.name')
               .joins(:category)
               .where("categories.name='News'")
  @grouped_posts = @posts.group_by(:category_name)     
{% endcodeblock %}


## Beware 

Beware that this can also happen when using gems that use eager loading. Making it harder to notice that is the issue. 

Hope this saves you some time. 
