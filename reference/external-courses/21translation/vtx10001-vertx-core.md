# Vert.X Core Manual

## 中英对照表

* Client：客户端
* Server：服务端
* Writing：写（有些地方译为开发）
* Event Bus：事件总线

## 正文

Vert.x内部的Java API集合称为**Vert.x Core**，[文档地址](https://github.com/eclipse/vert.x)。

Vert.x Core提供了下列功能

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

Core中这些功能相当底层——您不会在这里找到类似数据库访问、授权或高层Web应用的材料，这些材料您可以在**Vert.x ext** \[1\]（扩展包）中找到。

**Vert.X Core**很小、很轻量级，您可以仅仅使用您想要的（功能），它可以完全嵌入在已存在的应用里——它不强制您使用特殊的方式构造【Structure】您的应用，所以您可以（随时）使用Vert.X。

您可以使用Vert.X支持的其他语言中的任意一种（来开发）。但这儿有一个很酷的点——它不强制您直接使用Java API，（也可以使用）JavaScript或Ruby——毕竟，不同的语言有不同的约定和风格，若让Ruby开发员强制使用Java编码风格将会变得很奇怪。相反，它会为每一种语言自动生成和核心Java API等价的符合语言习惯的（API）。

从现在开始文中使用core代表**Vert.X Core**。

如果您在使用Maven或Gradle \[2\]，将下列依赖项添加到您的项目描述（文件）中`dependencies`节点【section】来访问**Vert.X Core**的API：

* Maven（您的`pom.xml`中）

```xml
<dependency>
  <groupId>io.vertx</groupId>
  <artifactId>vertx-core</artifactId>
  <version>3.4.1</version>
</dependency>
```

* Gradle（您的`build.gradle`中）

```groovy
dependencies {
    compile 'io.vertx:vertx-core:3.4.1'
}
```

接下来讨论Vert.X Core中不同的概念和特性。

### 故事从Vert.X开始

_注意：本文大部分内容专用于Java语言——若有需要可以切换到语言特定部分（手册中）。_

除非您可以与[Vert.X](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html)对象交流，否则在Vert.X大陆中您不能做太多的事情。

它（Vert.X对象）是Vert.X的控制中心，也是你如何去做几乎一切事情（的基础），包括创建客户端和服务端、获取事件总线的引用、设置定时器等其他很多事情。

那么您怎么去获取一个（Vert.X）实例呢？

## 引用

1. Vert.X的扩展包是Vert.X的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。
2. 两种常用的项目构建工具。

## 注释

## 结语



