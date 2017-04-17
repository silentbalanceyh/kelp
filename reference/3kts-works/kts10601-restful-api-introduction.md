# KTS10601 - Restful API基本说明

这是Tradeshift的开发人员API文档，这些API是基于REST的，它允许集成工程师处理很多相关资源任务，如管理业务文档、读取发送者/接受者信息、分发文档到接受者等。

## Tradeshift的基础

Tradeshift Apps支持在公司网络、个人用户之间沟通以及交换文档信息。账号（公司或用户）、网络、文档都属于Tradeshift App Platform的基础【Account、Network、Document】。

* **Account/Company**：【账号／公司】账号描述了一个可以和其他公司交换业务文档的公司，账号同样也称为公司或者公司账号，它不同于单独的用户，一个账号可通过其他账户信息或分支机构来描述、也可以有多个用户。
  * **Branches**：Tradeshift支持一个二级架构的账号（它可以包含子账号），当账号跟一些主账号（Master-Account）关联时，这些链接的账号称为分支。
  * **Master Branch/Master Account**：一个管理员账号可用来组织同属于一个组织下的子账号信息，ABC Global Enterprise主分支。只要它不是另一个账户的子分支，技术上任何一个账户都可以是主分支。
  * **Child Branch/Child Account**：若一个账号属于主分支，如：ABC Global Enterprise USA Division子分支。只要它不是另一个账户的子分支，技术上任何一个账号都可以是子分支。
  * **Account Info**：姓名、地址、国家、税务ID等。
  * **Users**：用户必须在公司账号内创建。当一个用户登录，这个用户都进入了公司账号，它可以访问公司账号中所有的文档和信息。如果是在一个主分支中创建，这个用户可以在这个主分支和属于它的子分支中进行切换。



