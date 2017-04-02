# Vert.x Core Manual

## 中英对照表

* Client：客户端
* Server：服务端
* Writing：编写（有些地方译为开发）
* Event Bus：事件总线
* Options：配置项
* Handler/Handle：处理器/处理

_注意：Vert.x和Vertx的区别：文中所有Vert.x概念使用标准单词Vert.x，而Vertx通常表示Java中的类：_`io.vertx.core.Vertx`_。_

## 正文

Vert.x内部的Java API集合我们称为**Vert.x Core**，[文档地址](https://github.com/eclipse/vert.x)。

Vert.x Core提供了下列功能

* 编写TCP客户端和服务端
* 编写HTTP客户端和支持WebSocket的服务端
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

**Vert.x Core**很小、很轻量级，您可以仅仅使用您想要的（功能），它可以完全嵌入在已存在的应用里——我们不强制您使用特殊的方式构造【Structure】您的应用，所以您可以（随时）使用Vert.x。

您可以使用Vert.X支持的其他语言中的任意一种（来开发）。但这儿有一个很酷的点——我们不强制您直接使用Java API，（也可以使用）JavaScript或Ruby——毕竟，不同的语言有不同的约定和风格，若让Ruby开发员强制使用Java编码风格将会变得很奇怪。相反，我们会为每一种语言自动生成和核心Java API等价的符合语言习惯的（API）。

从现在开始文中我们使用core代表**Vert.x Core**。

如果您在使用Maven或Gradle \[2\]，将下列依赖项添加到您的项目描述（文件）中`dependencies`节点【section】来访问**Vert.x Core**的API：

* Maven（您的`pom.xml`中）

```xml
<dependency>
  <groupId>io.vertx</groupId>
  <artifactId>vertx-core</artifactId>
  <version>3.4.1</version>
</dependency>
```

* Gradle（您的`build.gradle`中）

```gradle
dependencies {
    compile 'io.vertx:vertx-core:3.4.1'
}
```

接下来讨论Vert.x Core中不同的概念和特性。

### 故事从Vert.x开始

_注意：本文大部分内容专用于Java语言——若有需要可以切换到语言特定部分（手册中）。_

除非您可以与[Vert.x](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html)对象交流，否则在Vert.x大陆中您不能做太多的事情。

它（Vert.x对象）是Vert.x的控制中心，也是你如何去做几乎一切事情（的基础），包括创建客户端和服务端、获取事件总线的引用、设置定时器等其他很多事情。

那么您怎么去获取一个（Vert.x）实例呢？

如果您（的应用）嵌入了Vert.x，您可以通过下边代码简单创建一个实例：

```java
Vertx vertx = Vertx.vertx();
```

如果您要使用Verticle——

_注意：大部分应用将只会需要一个Vert.x实例，但如果您有需要也有可能创建多个Vert.x实例，如：隔离客户端和服务端的不同分组或事件总线。_

#### 创建Vertx对象时指定配置项

如果默认的（选项）不适合您，可以在创建Vertx对象时指定配置项：

```java
Vertx vertx = Vertx.vertx(new VertxOptions().setWorkerPoolSize(40));
```

[VertxOptions](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html)对象有很多设置，它允许您配置一些类似集群、高可用、池大小等其他各种设置，Javadoc中描述了所有设置的细节。

#### 创建集群的Vert.x对象

如果您想创建一个集群的Vert.x【Clustered Vert.x】（参考[event bus](http://vertx.io/docs/vertx-core/java/#event_bus)章节了解更多事件总线集群细节），通常您将使用异步协变方式【Variant】来创建一个Vertx对象。

这是因为一个集群中的不同Vert.X实例一起使用（协同运行）将会耗费一些时间（也许是几秒钟），这个时间段，它不想去阻塞调用线程，所以它会异步返回结果给您。

### 您Fluent吗？

您也许注意到前边的例子里使用了一个Fluent的API。

一个Fluent的API表示：多个方法形成链式结构一起按序调用。例如：

```java
request.response().putHeader("Content-Type", "text/plain").write("some text").end();
```

这是贯穿Vert.x的API中的一个通用模式，所以您要习惯它。

这样的链式调用会让您编写一些稍微冗长的代码。当然，如果您不喜欢Fluent的方式，它不强制您使用这种方式（编码），如果您更喜欢下边这种方式编写代码，您可以开心地忽略Fluent：

```java
HttpServerResponse response = request.response();
response.putHeader("Content-Type", "text/plain");
response.write("some text");
response.end();
```

### 不要Call它，它会Call您 \[3\]

Vert.x的API大部分都是事件驱动，也就是说您对Vert.x中发生的事情感兴趣时它会通过给您发送事件的方式Call您。

一些事件的例子如下：

* 触发一个计时器
* 一个Socket收到了一些数据
* 从磁盘中读取了一些数据
* 发生了一个异常
* HTTP服务器收到了一个请求

您可以使用Vert.x API提供的处理器来处理事件。例如每隔一秒收到一个计时器事件您可以这样做：

```java
vertx.setPeriodic(1000, id -> {
  // This handler will get called every second
  // 这个处理器将会每隔一秒被调用一次
  System.out.println("timer fired!");
});
```

又或者收到一个HTTP请求：

```java
server.requestHandler(request -> {
  // This handler will be called every time an HTTP request is received at the server
  // 服务器每次收到一个HTTP请求时这个处理器将被调用
  request.response().end("hello world!");
});
```

当Vert.x有一个事件要传给您的处理器时，一段时间后它会异步调用这个处理器。

这一点主导了Vert.x中一些重要的概念。

### 不要阻塞它



## 引用

1. Vert.x的扩展包是Vert.x的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。
2. 两种常用的项目构建工具。
3. Don't call us, we'll call you，Call斟酌了很久是翻译还是不翻译，”调用“在这个标题中听起来过于生硬，为了辅助理解没有翻译。

## 注释

## 结语



