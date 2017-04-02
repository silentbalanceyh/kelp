# Vert.x Core Manual

## 中英对照表

* Client：客户端
* Server：服务端
* Writing：编写（有些地方译为开发）
* Event Bus：事件总线
* Event Loop：事件循环
* Reactor：反应堆
* Options：配置项
* Handler/Handle：处理器/处理
* Block：阻塞

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

这是因为一个集群中的不同Vert.X实例一起使用（协同运行）将会耗费一些时间（也许是几秒钟），这个时间段，我们不想去阻塞调用线程，所以我们会异步返回结果给您。

### 您Fluent吗？

您也许注意到前边的例子里使用了一个Fluent的API。

一个Fluent的API表示：多个方法形成链式结构一起按序调用。例如：

```java
request.response().putHeader("Content-Type", "text/plain").write("some text").end();
```

这是贯穿Vert.x的API中的一个通用模式，所以您要习惯它。

这样的链式调用会让您编写一些稍微冗长的代码。当然，如果您不喜欢Fluent的方式，我们不强制您使用这种方式（编码），如果您更喜欢下边这种方式编写代码，您可以开心地忽略Fluent：

```java
HttpServerResponse response = request.response();
response.putHeader("Content-Type", "text/plain");
response.write("some text");
response.end();
```

### 不要Call我，我会Call您 \[3\]

Vert.x的API大部分都是事件驱动，这意味着您对Vert.x中发生的事情感兴趣时它会通过给您发送事件的方式Call您。

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

稍后当Vert.x有一个事件要传给您的处理器时，它会异步调用这个处理器。

这一点引导我们（去理解）Vert.x中一些重要的概念。

### 不要阻塞我

除了很少的特例（如以“Sync"结尾的某些文件系统操作），Vert.x中的所有API都不会阻塞调用线程。

如果可以立即提供结果，它将立即返回，否则您将在稍后提供一个处理器来接收事件。

因为没有一个Vert.x API会阻塞线程，这意味着您可以使用Vert.x通过少量线程来处理大量并发。

若使用传统的阻塞式API（编码），调用线程可能会被阻塞：

* 从Socket中读取数据
* 写数据到磁盘
* 发送消息给收件人并等待回复
* ...其他很多情况

在上述所有情况下，当您的线程正在等待结果时它不能做任何事情——此时，这些线程实际上是无用的。

这意味着如果您想使用阻塞式API处理大量并发，则您需要大量线程来防止应用程序停止/挂起【Halt】。

线程本身就需要的内存（如它们的堆栈）和上下文切换而言，会具有开销。

对于许多现代应用程序所要求的并发级别，阻塞的方式将会难于扩展。

### Reactor和Multi-Reactor

我们前边提过Vert.x的API都是事件驱动的——当事件可用时Vert.x 将这个事件传给处理器。

在多数情况下，Vert.x使用一个称为事件循环【Event Loop】\[4\]的线程来调用您的处理器。

Vert.x或应用程序块中没有任何阻塞，这个事件循环可以快速地运行，以便在事件到达时向不同的处理器成功传递事件。

因为没有阻塞，一个单事件循环可以在短时间内传递大量的事件。例如，一个单事件循环可以很快处理数千个HTTP请求。

我们称之为[反应堆【Reactor】模式](https://en.wikipedia.org/wiki/Reactor_pattern)。

您之前也许听说过它——例如Node.js实现了这种模式。

一个标准的反应堆实现中，有一个单事件循环会轮询执行，当所有事件到达时将这些事件传递给所有处理器。

一个线程的麻烦就是它在任何一个时间只能在一个单一的核上运行，如果您希望单线程反应堆应用（如您的Node.js应用）扩展到您的多核服务器上，则不得不启动并且管理许多不同的过程。

这一点Vert.x的工作有所不同，每个Vertx实例不是维护的一个事件循环，而是维护的多个。默认情况下，我们会根据机器上可用的核数量来选择事件循环数，而这个（设置）可以被覆盖。

与Node.js不同，这意味着单个Vertx进程可以跨服务器扩展。

我们将这种模式称为多反应堆模式【Multi-Reactor】，将它和单线程反应堆模式进行区分。

_注意：即使一个Vertx实例维护了多个事件循环，任何特定的处理器永远不会同时执行，大部分情况下（除开_[_Worker Verticle_](http://vertx.io/docs/vertx-core/java/#worker_verticles)之_外）它们总是在完全相同的事件循环中被调用。_

### 黄金法则——不要阻塞事件循环

我们已经知道，Vert.x的API都是非

## 引用

1. Vert.x的扩展包是Vert.x的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。
2. 两种常用的项目构建工具。
3. Don't call us, we'll call you，Call斟酌了很久是翻译还是不翻译，”调用“在这个标题中听起来过于生硬，为了辅助理解没有翻译。
4. Event Loop在文中翻译成事件循环。

## 注释

## 结语



