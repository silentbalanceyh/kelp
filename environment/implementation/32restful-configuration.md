# Restful 配置教程

## 0.Introduction

一个完整的Restful配置主要涉及下列几张表：

* Route：Vert.x中专用路由配置表
* Uri：Restful的Uri配置表
* Script：Uri中脚本引擎配置表
* Rule：Uri中的转换器/验证器规则配置表

如果在Uri的配置中使用了新的消息地址，则需要配置下边两张表：

* Address：消息地址绑定表
* Verticle：Vert.x中的Verticle专用配置表，对应DeploymentOptions

## 1. Table Content

* [MTS10001 - Restful配置默认值](/environment/implementation/32restful-configuration/vtx10001-default-values.md)



