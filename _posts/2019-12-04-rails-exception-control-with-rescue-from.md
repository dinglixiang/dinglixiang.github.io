---
title: "Rails: exception control with rescue_from"
layout: post
date: 2019-12-04 14:16
guid: urn:uuid:f5112968-f33b-4505-92db-cf2d05ba555e
author: "Lixiang"
tags:
  - Rails
  - Arch
---

When we talk about the software architechure, sometimes the `Exception Control` concept will be metioned.

In Rails, `rescue_from` is a very useful method to implement the `Exception Control`. It will be better if combine with the `Service`.

Example like the follows.

### Controller
In the seperate controllers, we only rescue the internal customized business exceptions, clean the code.

```ruby
class UsersController < ApplicationController
  rescue_from EmailHasBeenUsedError, MobileHasBeenUsedError, with: :render_error

  def create
    User::CreateService.new(create_params).call

    render status: :ok
  end

  private

  def create_params
    params.require(:user).permit(:email, :mobile, :nickname, :password)
  end

  def render_error(e)
    render json: { errors: e.message }, status: :unprocessable_entity
  end
end
```

Also, we can rescue the customized exception with the action method.

```ruby
def create
  User.create!(create_params)

  render status: :ok
rescue EmailHasBeenUsedError => e
  render json: { errors: e.message }, status: :unprocessable_entity
end
```


### Service
In service, we can check the pre-conditions, combine multiple models to process the business logic and so on.

The point is that we can raise the customized exception with any abnormal case, like email has been used error. And
these exceptions will be catched in controller, it seems clearfy for response.

```ruby
module User
  class CreateService
    attr_reader :create_params

    def initialize(create_params)
      @create_params = create_params
    end

    def call
      # Just for example
      raise EmailHasBeenUsedError, 'email has been used.' if User.exists?(email: create_params[:email])
      raise MobileHasBeenUsedError, 'mobile has been used.' if User.exists?(mobile: create_params[:mobile])

      ActiveRecord::Base.transaction do
        User.create!(create_params)
        ...
      end
    end
  end
end
```

### END

In the end, the above is my simple knowledge about `Exception Control`.

PS: we can also ignore the performance about `Exception Control`,
becase it is very low percent to raise the exceptions and does not has an effect on the performance. But if you raise it often, maybe the business has some problems.

FYI:

- [rescue_from method](https://apidock.com/rails/ActiveSupport/Rescuable/ClassMethods/rescue_from)
