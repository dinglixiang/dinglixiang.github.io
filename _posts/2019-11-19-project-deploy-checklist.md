---
title: 项目部署Checklist
layout: post
date: 2019-11-19 20:06
guid: urn:uuid:65a526a4-88e6-4fb3-9fac-eb9a43306dfc
author: "Lixiang"
tags:
  - Management
---

在项目开发过程中，Checklist是一个能够很好辅助项目风控的工具。

介绍一下自己一直在写的部署Checklist。

### 思路
类比开发，开发的时候，我会通过 `脑图 + 思维导图`的形式，细化整个业务需求，完善开发思考的维度和细节。

在写项目部署Checklist的时候，会参考开发的任务，结合前端、测试以及业务等各方面，代入思考，譬如`我作为测试，应该要测哪些内容?`。

一般我会分几个角度来写Checklist。

#### 1.业务需求
首先要明确业务需求，本次迭代的主要内容是哪些，修复的Bug有哪些，一一列举，防止遗漏

#### 2.开发阶段
带入思考项目参与人员的角色， 思考有哪些重要里程碑和注意事项。

譬如:

- 作为前端，开发时有哪些风险，改公用组件的影响范围有哪些，譬如小程序中某个Feature需要提前提审，以便能够测试等
- 作为后端，需要不需要创建表，需不需要数据迁移，上线后的操作有哪些，这些操作有没有顺序要求等
- 作为测试，测试范围是哪些，哪些是冒烟测试用例，哪些是边缘测试用例等

#### 3.测试阶段
结合项目排期，确定各个Feature的提测时间点，测试人员结合开发人员给予测试范围后，测试和记录问题(JIRA)，问题中需要记录问题、操作步骤、期望结果、分析和问题定位。

#### 4.项目管控
从项目管控角度出发，为了按时高质量的完成项目交付，需要`以终为始`，从结束时间开始倒推各个重要时间点，包含Kickoff时间、Test Meeting时间、联调时间、提测时间、测试完成时间、Code Review时间、上线发布时间。

项目过程中，也有很多重要事件，譬如哪些功能要提前提测、上线前后需要有哪些操作等。

从时间和事件上出发，紧跟两者，能够帮助项目在正常的轨迹上运转。
#### 5.项目发布后
以上的内容，都是为了确保项目能够按时高质量交付，是发布之前的准备工作。

我也建议思考项目发布后，有哪些内容，譬如前端清理CDN、发布后监控服务运行状态(如秒杀上线)、Feature发布一段时间后进行数据赋能等


### 示例

<img src="/img/0902_deploy_checklist.png"/>