# KTS10601 - Restful API基本说明

这是Tradeshift的开发人员API文档，这些API是基于REST的，它允许集成工程师处理很多相关资源任务，如管理业务文档、读取发送者/接受者信息、分发文档到接受者等。

## Tradeshift的基础

Tradeshift Apps支持在公司网络、个人用户之间沟通以及交换文档信息。账号（公司或用户）、网络、文档都属于Tradeshift App Platform的基础【Account、Network、Document】。

* **Account/Company**：【账号／公司】账号描述了一个可以和其他公司交换业务文档的公司，账号同样也称为公司或者公司账号，它不同于单独的用户，一个账号可通过其他账户信息或分支机构来描述、也可以有多个用户。
  * **Branches**：Tradeshift支持一个二级架构的账号（它可以包含子账号），当账号跟一些主账号（Master-Account）关联时，这些链接的账号称为分支。
  * **Master Branch/Master Account**：一个管理员账号可用来组织同属于一个组织下的子账号信息，ABC Global Enterprise主分支。只要它不是另一个账户的子分支，技术上任何一个账户都可以是主分支。
  * **Child Branch/Child Account**：若一个账号属于主分支，如：ABC Global Enterprise USA Division子分支。只要它不是另一个账户的子分支，技术上任何一个账号都可以是子分支。
  * **Account Info**：姓名、地址、国家、税务ID等。
  * **Users**：用户必须在公司账号内创建。当一个用户登录，这个用户都进入了公司账号，它可以访问公司账号中所有的文档和信息。如果是在一个主分支中创建，这个用户可以在这个主分支和属于它的子分支中进行切换。如果在一个子分支中创建，那这个账号不能切换到其他分支中。
  * **User Info**：姓名、邮件、消费限制、汇报关系。
* **Network**：用户用于连接其他用户的多对多的集合，所有的Tradeshift的账号都连接到了Tradeshift的网络，也就是说任何一个账号都可以邀请它的业务伙伴加入到自己的网络中（类似于社交网络）。
  * **Connections**：两个账号之间的链接，一个链接在两个账号之间交换文档时必须是激活的。要开启一个链接，一个账号必须对另外一个账号发送链接请求，而接收请求这个账号必须要接收该请求。
  * **External Connection**：（俗称“手动连接“）您为要连接的业务伙伴创建一个“占位符”账户，您填写了账户的个人资料、包括姓名、电子邮件、地址信息等。若业务伙伴尚未接收连接请求，或者改账户没有激活。您也许仍然会更新账号信息如姓名、电子邮件等。一般情况下，外部连接是暂时的，一旦业务伙伴激活了“占位符”账户，该连接将会转变成Tradeshift连接——您将无法再更改账户资料信息。
  * **Tradeshift Connection**：（俗称“主动连接”）公司被邀请连接并已经接受邀请，受邀请的公司准备发送和接收文件，受邀请公司拥有其名称和电子邮件账户信息——您不能更改账户信息。
  * **Connection Property**：用于表述连接的自定义属性。如：买方公司可以将其内部供应商ID存储为每个供应商连接上的连接数据，对称地，另一方的供应商可以将其内部客户ID存储为连接属性。
  * **Group**：一个账户可以根据自己创建的组来分类连接，命名和定义由该账户决定。如，买方公司可能会有两个供应商：供应商需要采购订单，供应商不需要采购订单。
* **Documents/Documentfiles**：在招标、报价、购买、运输、发票等业务交易中，公司之间交换业务的文件。

  * **UBL**：一个标准的电子XML商业文件库、如采购订单和发票。这是Tradeshift使用的首选标准文档格式，标准由OASIS（结构化信息提升标准组织）开发，有关信息信息，可访问：[https://www.oasis-open.org/committees/ubl](https://www.oasis-open.org/committees/ubl)
  * **TSUBL**：TSUBL包含UBL 2.0标准下的文档子集，从此链接下载TSUBL规范和样本文件到TSUBL规范。或：转到[http://integrate.tradeshift.com/](http://integrate.tradeshift.com/)：。然后在”支持的格式“部分中单击”UBL“。

  * **Tenant Isolation**：租户（账户）之间是不可直接共享数据的，有与每个账户关联的存储。发送文件时，将在接收者的账户中创建并呈现发件人的文档的副本。因此，租户之间没有直接的数据共享。根据上下文，文档的一个副本的操作可能会影响或不影响其他副本。例如：

    * Supplier（Sender）：供应商（发件人）删除发送的发票将使发票在供应商账户中消失。然而，买方（接收方）仍然可以看到买方账户中的发票，因为买方对该发票的副本不受影响——这种情况下，对发票人的发票副本的操作不会影响收件人副本。
    * Buyer（Receiver）：买方（收件人）将收到的发票的状态更新为”已接受“，这将触发向供应商（发件人）账户的状态消息，将发票的供应商副本更新为”已接受“。这种情况下，对发票的接收者副本的操作会自动影响发件人副本。

## 核心概念/术语

* **REST API**：Tradeshift的API是基于HTTPS、REST和OAuth的，它允许开发人员使用HTTP方法PUT/POST/GET/DELETE来访问”资源“如：分发文档、创建用户、和其他账户连接。它可以被Tradeshift和第三方开发人员使用，完整的API可参考：[https://api.tradeshift.com/tradeshift/rest/external/doc](https://api.tradeshift.com/tradeshift/rest/external/doc)
* **OAuth**：Tradeshift同时支持OAuth1和OAuth2，开发人员可基于此访问用户资源，更多OAuth信息参考：[http://oauth.net/](https://www.google.com/url?q=http://oauth.net/&sa=D&ust=1465606866900000&usg=AFQjCNFVoq3l3yHpvjwjcQPTcVjj98vcRg)
* **UUID**：全局唯一标示符用来表示Tradeshift中的实体，根据Wikipedia中的描述，UUID是一个软件标识标准。Tradeshift使用具有插入连字符的十六进制文本的规范格式。如：de305d54-75b4-431b-adb2-eb6b9e546014。这儿有一些JavaScript和免费的站点生成UUID。例如：[https://www.uuidgenerator.net/](https://www.google.com/url?q=https://www.uuidgenerator.net/&sa=D&ust=1465606866902000&usg=AFQjCNGhka_7OB4zo7oGxMHeZbNsAmM8Ig)和[https://github.com/broofa/node-uuid](https://www.google.com/url?q=https://github.com/broofa/node-uuid&sa=D&ust=1465606866903000&usg=AFQjCNGsFXLyJaAqGxqObLsQ7TkUX4y_YQ)
* **Standards**：Tradeshift遵循适用于国家代码、货币代码、税码等代码的标准。当使用Tradeshift文档或参数时，开发人员需要使用相同的代码。以下是Tradeshift遵循的标准清单（不详尽），有关完整的标准和值集，请参考：[TSUBL](https://www.google.com/url?q=http://jsi9mwa10eq3hertdc3j3ou.wpengine.netdna-cdn.com/wp-content/uploads/2015/11/TSUBL_20151118.zip&sa=D&ust=1465606866904000&usg=AFQjCNGLpWLinRZxap-EfuRHiWIyjadEIg)[ specifications](https://www.google.com/url?q=http://jsi9mwa10eq3hertdc3j3ou.wpengine.netdna-cdn.com/wp-content/uploads/2015/11/TSUBL_20151118.zip&sa=D&ust=1465606866905000&usg=AFQjCNGPpnYyfDlYm19BL76T4DX82BE6Qg)



