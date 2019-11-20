---
title: "Rails: unscoped method"
layout: post
date: 2019-11-20 16:39
guid: urn:uuid:1a60aa40-aa4c-4dff-a7c5-32adb00b9e10
author: "Lixiang"
tags:
  - Rails
---

最近业务迭代过程中，再次使用了 `default_scope`，踩了 `unscoped`的坑。

ActiveRecord提供了`unscoped`方法去清除方法调用之前的条件设定。

>Returns a scope for the model without the previously set scopes.

```ruby
class FormSetting < ActiveRecord::Base
  has_many :forms
end

class Form < ActiveRecord::Base
  enum category: { log: 0, checkout: 1 }

  default_scope { log }

  belongs_to :form_setting
end


form_setting.forms.unscoped.checkout
```

针对上面的代码，按我的错误理解，是仅去除`default_scope`的log条件，变成查询checkout类型的forms数据。

但实际结果如下:

```sql
form_setting.forms.first
#> Form Load (1.6ms)  SELECT  "forms".* FROM "forms" WHERE "forms"."category" = $1 AND "forms"."form_setting_id" = $2  ORDER BY "forms"."id" ASC LIMIT 1  [["category", 0], ["form_setting_id", 111]]

form_setting.forms.unscoped.checkout.first
#> Form Load (2.3ms)  SELECT  "forms".* FROM "forms" WHERE "forms"."category" = $1  ORDER BY "forms"."id" ASC LIMIT 1  [["category", 1]]
```

`unscoped`将`form_setting`的条件也去除了，导致数据查询错误。我的预想是错误的，从SQL层面可以更直观的看到，unscoped会将调用链之前的where条件全都去掉，仅使用unscoped调用之后的条件。

`form_setting.forms.unscoped`等同于`Form.unscoped`。

为了达到调用链上仅去除`default_scope`的目的，可以将查询修改成如下方式：

~~form_setting.forms.unscoped.checkout~~

```ruby
Form.unscoped.where(form_setting: form_setting).checkout
```

FYI:

- [unscoped method](https://api.rubyonrails.org/classes/ActiveRecord/Scoping/Default/ClassMethods.html#method-i-unscoped)
