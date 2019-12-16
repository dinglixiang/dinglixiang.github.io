---
title: "How to design dynamic form"
layout: post
date: 2019-12-16 22:24
guid: urn:uuid:68ded769-14e8-458d-a292-623a84c9a4aa
author: "Lixiang"
tags:
  - Arch
---

业务开发中，会遇到各种表单提交。表单格式也会根据业务场景定制化，动态表单是基于原有表单场景的扩展，能够适应多种业务场景。

动态表单，顾名思义是能够动态添加字段、动态渲染表单并提交数据。

实现动态表单的方式也有很多，下面我简单列举之前调研的两个方案，其主要区别是针对表单项的处理方式不同

### 关系数据表存储表单定义
参考[Entity–attribute–value model (EAV) ](https://en.wikipedia.org/wiki/Entity%E2%80%93attribute%E2%80%93value_model)的方案，设计出如下内容：

#### forms(表单模板)

字段名 | 字段类型 | 说明 | 备注 |
----   | ----     | --   | -- |
name         | string   | 表单名称     |
logo         | string   | logo         |
status       | string   | 状态         | 启用：online, 停用：offline
source       | integer  | 来源         | enum 0: 公用模板，1: 自定义模板
fields_limit | integer  | 字段数量上限 |
fields_count | integer  | 字段数量     |
deleted_at   | datetime | 删除时间     |
created_at   | datetime | 创建时间     |
updated_at   | datetime | 更新时间     |

#### form_fields表

字段名|	字段类型|	说明|	备注 |
----   | ----     | --   | -- |
form_id    | integer  | form ID   |
form_type  | string   | form 类型 | 多态
name       | string   | 字段名称  |
category   | string   | 字段类型  | 文本：text；数字：number； 单选框：radio；多选框：checkbox
required   | bool     | 是否必填  | 是：true, 否：false
values     | json     | 选择框值  | [{name: '美团'}, {name: '大众点评'}]
order      | integer  | 排序权重  |
deleted_at | datetime | 删除时间  |
created_at | datetime | 创建时间  |
updated_at | datetime | 更新时间  |

优点：
- 分表存储，自定义字段内容清晰；
- 便于细化统计分析；

缺点：
- 多表存储，维护成本高，模板多条字段同时更新，需要同步更新多条记录；
- 对于模板一次更新提交，判断规则复杂，需要判断出字段更新、新增字段、删除字段，并根据不同类别做相应的处理；

若采用Rails的[accepts_nested_attributes_for](https://api.rubyonrails.org/classes/ActiveRecord/NestedAttributes/ClassMethods.html#method-i-accepts_nested_attributes_for)处理此场景，能够大大减少复杂性


### JSON Schema存储表单定义
利用Postgres强大的jsonb类型以及JSON Schema的完整标准，将动态自定义表单进行了一定简化，也满足了业务需求。

修改原来的form表设计，将字段信息，通过json的格式存储到一个字段内，而不是单独的数据表。

表单内容存储分为3部分

1. schema：表单以及表单项的属性等信息
2. ui schema: 渲染表单的配置
3. editor schema: 编辑schema的schema

#### forms(表单模板)

字段名 | 字段类型 | 说明 | 备注|
----   | ----     | --   | -- |
name         | string   | 表单名称     |
logo         | string   | logo         |
status       | string   | 状态         | 启用：online, 停用：offline
source       | integer  | 来源         | enum 0: 公用模板，1: 自定义模板
data         | jsonb    | 表单内容     | `{"schema":{"title":"","type":"object","required":["customized"],"properties":{"customized":{"title":"","type":"object","required":["email","notes"],"properties":{"email":{"type":"string","title":"电子邮箱","format":"active"},"notes":{"type":"string","title":"买家留言","format":"active","max_length":500}}}}},"ui_schema":{"customized":{"email":{"ui:widget":"input"},"notes":{"ui:widget":"input"},"ui:order":["email","notes","*"]},"ui:order":["customized"]}}`
fields_limit | integer  | 字段数量上限 |
fields_count | integer  | 字段数量     |
deleted_at   | datetime | 删除时间     |
created_at   | datetime | 创建时间     |
updated_at   | datetime | 更新时间     |

优点：
- 在一个表中，便于维护与管理；
- 自定义字段发生修改，更新 data 字段内容即可，维护成本低；

缺点：
- 字段删除时，无法回溯到源数据

### END
对比了上述两者方案，以及实现成本，选择了JSON Schema的方案。目前除了删除字段的问题外，暂无其他问题出现。

FYI:

- [Rails 如何动态定义表结构？已上传了我的方法。](https://ruby-china.org/topics/26592)
- [关于扩展字段的设计问题](https://ruby-china.org/topics/28380#10)
- [FormCore —— 动态表单系统的一种参考实现 (带一点私货技巧)](https://ruby-china.org/topics/33098)
