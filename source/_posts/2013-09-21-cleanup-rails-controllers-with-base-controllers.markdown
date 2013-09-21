---
layout: post
title: "DRY up your controllers with base controllers"
date: 2013-09-21 04:44
comments: true
categories: rails, Activecontroller
---

Controllers are just basic ruby classes that inherit from ActionController::Base. This means you can manage your controllers just as you would manage a ruby class. DRY up your controllers by moving supporting methods into a base controller.

<!-- more -->

Obviously, we can move methods into the Application controller. But we may not need these methods across the whole application. A perfect example of this is when we are creating a site that has public and private sections of the site. 

Example 1: 

``` ruby
class ApplicationController < ActionController::Base
  before_filter :authenticate_user!
  
  # other methods 
end
```  

The problem with this is we would have to do a skip_before_filter on every controller we want public. Or conversely we would have to call the before_filter on all private pages. 

I prefer to create a namespace for the private sections by creating a directory in Rails root app/controllers. Let's call this directory admin. Then create a new controller within the admin directory for users.

Example 2: 

``` ruby
class Admin::UsersController < ActionController::Base
  before_filter :authenticate_user!
  
  # other methods 
end
```

Now we can access the protected controller by going to http://localhost:3000/admin/users But now we still have to apply a before_filter on any added controllers within admin. I think a better way to handle this is to create a base controller within the admin directory that the admin controllers can inherit from. 

Example 3:

``` ruby
class Admin::BaseController < ActionController::Base
  before_filter :authenticate_user!
  
  # other methods 
end

class Admin::UsersController < Admin::BaseController
  # other methods 
end
```

This has the added benefit of being able to add any methods that can be used across the admin in a shared controller. 

However, there is no reason we must limit ourselves to only the admin. I find many times I have methods I need to use across specific controllers. This is a perfect opportunity to DRY up those controllers, by namespacing and creating a base controller. 

Example 4: 

``` ruby
class Reports::BaseController < ActionController::Base
  # methods for all reports 
end

class Reports::StandardController < Reports::BaseController
  # other methods 
end

class Reports::CustomController < Reports::BaseController
  # other methods 
end
``` 


This is nothing groundbreaking or revolutionary. But like many things in ruby and Rails, simple and basic saves the day. 

