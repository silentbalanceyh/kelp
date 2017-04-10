# KT10100 - Web Service基础

## 1. Introduction

Web Service是一个平台独立的、低耦合的、自包含的、基于可编程的Web应用程序，它使用了开放的XML标准来描述、发布、发现、协调和配置这些应用程序，用于开发分布式的互操作应用。

Web Service实际上是基于Internet互联网的一种通讯技术，它涉及的规范如下

* XML（Extensible Markup Language）：可扩展标记型语言
* SOAP（Simple Object Access Protocol）：【XML格式】简单对象访问协议（Web Service专用通讯协议）
* WSDL（Web Services Description Language）：【XML格式】用于说明一组SOAP消息以及如何交换这些消息
* UDDI（Universal Description, Discovery, and Integration）：主要针对Web服务供应商和使用者的协议——在用户能调用Web服务器之前必须确定这个服务内包含的方法已经在服务注册中心注册过。UDDI是一种根据描述文档来引导系统查找相应服务的机制，它本身使用SOAP消息机制（标准XML/HTTP）来发布、编辑、浏览、查找注册信息，同样使用XML来封装不同类型的数据，并发送注册中心或从注册中心返回。





