---
title: "Resolve devise's unlock_token performance issue"
layout: post
date: 2020-07-08 17:56
guid: urn:uuid:b1eec65a-3c59-4580-881f-ed5c2d79ab39
author: "Lixiang"
tags:
  - Rails
---
Devise默认的unlock逻辑在数据量较大的情况下，unlock会出现慢查询的问题。

### 背景

数据库User表数据庞大，慢查询中经常会出现如下的慢查询语句：

```sql
SELECT  "users".* FROM "users" WHERE "users"."unlock_token" = 'xxx' ORDER BY "users"."id" ASC LIMIT 1;
```

调查发现，这是由Devise lockable中unlock的逻辑触发的。


### 解决方案

#### 1. 添加`unlock_token`索引

出现慢查询，自然而然的会想到分析SQL、添加索引，此次遇到的问题SQL语句简单，仅有一个`unlock_token`的查询条件

但基于以下原因，添加索引不是一个好的选择

- users表超大，添加索引时要注意耗时
- 索引占用空间会增加，且`unlock_token`是一个临时值，每次lock都会更新
- `unlock_token`存在为null的情况

#### 2. 修改查询逻辑
是否能够通过ID和`unlock_token`联合查询，通过主键ID计划查询解决

Devise提供了覆盖controller等默认逻辑的方式，通过覆盖 `Devise::UnlocksController#show`解决`unlock_token`慢查询问题

#### 1) 修改邮件内容
运行以下命令，生成devise views文件，其中包含`unlock_instructions.html.erb`的邮件模板

```shell
rails g devise:views
```

修改`unlock_instructions.html.erb`其中的unlock url，添加id参数 `id: @resource.id`，示例如下：

```ruby
%p= link_to 'Unlock my account', unlock_url(@resource, id: @resource.id, unlock_token: @token)
```


#### 2) 覆盖unlocks controller
运行一下命令，生成默认的覆盖controllers

```shell
rails g devise:controllers users
```

此命令会在`app/controller/users`目录下生成多个controller
```ruby
confirmations controller
passwords controller
registrations controller
sessions controller
unlocks controller
omniauth_callbacks controller
```

修改其中的unlocks controller

```ruby
class Users::UnlocksController < Devise::UnlocksController

  # GET /resource/unlock?id=xxx&unlock_token=abcdef
  def show
    self.resource = if resource_class.name == 'User' && params[:id].present?
                      unlock_token = Devise.token_generator.digest(self, :unlock_token, params[:unlock_token])

                      lockable = User.find_or_initialize_with_errors([:id, :unlock_token], { id: params[:id], unlock_token: unlock_token })
                      lockable.unlock_access! if lockable.persisted?
                      lockable.unlock_token = params[:unlock_token]
                      lockable
                    else
                      # 兼容部署上线前发送的unlock邮件
                      resource_class.unlock_access_by_token(params[:unlock_token])
                    end

    yield resource if block_given?

    if resource.errors.empty?
      set_flash_message :notice, :unlocked if is_flashing_format?
      respond_with_navigational(resource) { redirect_to after_unlock_path_for(resource) }
    else
      respond_with_navigational(resource.errors, status: :unprocessable_entity) { render :new }
    end
  end
end
```
值得提及的是

- if条件下的执行语句

该语句是参考[`unlock_access_by_token`源码](https://github.com/heartcombo/devise/blob/62703943bef75aba09ec3e346aba4c9159300ecd/lib/devise/models/lockable.rb#L181)修改的

`unlock_access_by_token`的查询逻辑是，仅根据unlock token进行数据查询，而我们需要改造成根据id和unlock token联合进行查询。
所以将其中的`find_or_initialize_with_error_by(:unlock_token, unlock_token)`替换为`User.find_or_initialize_with_errors([:id, :unlock_token], { id: params[:id], unlock_token: unlock_token })`

`find_or_initialize_with_error_by`方法根据一个属性查询一条数据，其内部还是调用`find_or_initialize_with_errors`方法。详情可点击[`find_or_initialize_with_error_by`源码](https://github.com/heartcombo/devise/blob/2c1b5fb240611ba976c1d3917f271707e422f0bc/lib/devise/models/authenticatable.rb#L280)阅读

- `set_flash_message`方法
[devise 4.0.0.rc2](https://github.com/heartcombo/devise/blob/6991ff4eb42bb8a197f8c83ed9bcf46d50392083/CHANGELOG.md#400rc2---2016-03-09)新增并使用了`DeviseController#set_flash_message!`方法，由于老项目使用的是3.5.10版本，所以参考3.5.10的源码后，修改为使用`set_flash_message`方法


#### 3) 修改routes
`config/routes.rb`中，增加如下内容

```ruby
 Rails.application.routes.draw do
   devise_for :users, controllers: {
     unlocks: 'users/unlocks'
   }
end
```

至此，修改unlock的逻辑完成，unlock token慢查询的问题得到解决。

FYI:
- [devise 101](http://domckellar.com/2016/12/12/devise_101/)
