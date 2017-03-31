# Vert.X Core Manual

## 中英对照表

| 英文 | 中文 | 英文 | 中文 | 英文 | 中文 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| Client | 客户端 | Writing | 写、开发 | Application | 应用 |
| Server | 服务端 | Authorization | 授权 |  |  |
| Event Bus | 事件总线 |  |  |  |  |

## 正文

Vert.X内部的Java API集合称为**Vert.X Core**，文档地址：[Repository](#)。

Vert.X Core提供了下列功能

* 开发TCP客户端和服务端
* 开发HTTP客户端和支持WebSocket的服务端
* 事件总线【Event Bus】
* 共享数据——本地的Map和分布式集群Map
* 周期性、延迟性行为
* 部署和撤销Verticle实例
* 数据报路【Datagram Sockets】
* DNS客户端
* 文件系统访问
* 高可用性
* 集群

Core中这些功能相当底层——您不会在这里找到类似数据库访问、授权或高层Web应用的材料，这些材料您可以在**Vert.X ext**\[1\]（扩展包）中找到。

**Vert.X Core**很小、很轻量级，您可以仅仅使用您想要的（功能），它可以完全嵌入在已存在的应用里——它不强制您使用特殊的方式设计【Structure】您的应用，所以您可以（随时）使用Vert.X。

您可以使用Vert.X支持的其他语言中的任意一种（来开发）。但这儿有一个很酷的点——

## 引用

1. Vert.X的扩展包是Vert.X的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。



