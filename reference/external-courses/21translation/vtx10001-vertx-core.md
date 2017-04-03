# Vert.x Core Manual

## 中英对照表

* Client：客户端
* Server：服务器
* Writing：编写（有些地方译为开发）
* Reactor：反应堆
* Options：配置项
* Context：上下文环境
* Undeploy：撤销（对应部署）
* Unregister：注销（对应注册）
* Destroyed：销毁
* Handler/Handle：处理器/处理，有些特定处理器未翻译，如Completion Handler等。
* Block：阻塞
* Out of Box：标准环境（开箱即用）
* Timer：计时器
* Worker Pool：工作线程池，大部分地方未翻译
* Sender：发送者
* Consumer：消费者
* Receiver/Recipient：接收者
* Entry：条目（一条key=value的键值对）
* Map：动词翻译成“映射”，名词为数据结构未翻译
* Logging：动词翻译成“记录”，名词翻译成日志器
* Trust Store：受信存储

_注意：Vert.x和Vertx的区别：文中所有Vert.x概念使用标准单词Vert.x，而Vertx通常表示Java中的类：_`io.vertx.core.Vertx`_。_

## 正文

Vert.x内部的Java API集合我们称为**Vert.x Core**，[文档地址](https://github.com/eclipse/vert.x)。

Vert.x Core提供了下列功能

* 编写TCP客户端和服务器
* 编写HTTP客户端和支持WebSocket的服务器
* Event Bus（事件总线）
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

它（Vert.x对象）是Vert.x的控制中心，也是您如何去做几乎一切事情（的基础），包括创建客户端和服务器、获取事件总线的引用、设置定时器等其他很多事情。

那么您怎么去获取一个（Vert.x）实例呢？

如果您（的应用）嵌入了Vert.x，您可以通过下边代码简单创建一个实例：

```java
Vertx vertx = Vertx.vertx();
```

如果您要使用Verticle——

_注意：大部分应用将只会需要一个Vert.x实例，但如果您有需要也有可能创建多个Vert.x实例，如：隔离客户端和服务器的不同分组或事件总线。_

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
* 发送消息给接收者并等待回复
* ...其他很多情况

在上述所有情况下，当您的线程正在等待结果时它不能做任何事情——此时，这些线程实际上是无用的。

这意味着如果您想使用阻塞式API处理大量并发，则您需要大量线程来防止应用程序停止/挂起【Halt】。

线程本身就需要的内存（如它们的堆栈）和上下文切换而言，会具有开销。

对于许多现代应用程序所要求的并发级别，阻塞的方式将会难于扩展。

### Reactor和Multi-Reactor

我们前边提过Vert.x的API都是事件驱动的——当事件可用时Vert.x 将这个事件传给处理器。

在多数情况下，Vert.x使用一个称为**Event Loop**的线程来调用您的处理器。

Vert.x或应用程序块中没有任何阻塞，这个**Event Loop**可以快速地运行，以便在事件到达时向不同的处理器成功传递事件。

因为没有阻塞，一个Event Loop可以在短时间内传递大量的事件。例如，一个单独的**Event Loop**可以很快处理数千个HTTP请求。

我们称之为[反应堆【Reactor】模式](https://en.wikipedia.org/wiki/Reactor_pattern)。

您之前也许听说过它——例如Node.js实现了这种模式。

一个标准的反应堆实现中，有一个单独的Event Loop会轮询执行，当所有事件到达时将这些事件传递给所有处理器。

一个线程的麻烦就是它在任何一个时间只能在一个单一的核上运行，如果您希望单线程反应堆应用（如您的Node.js应用）扩展到您的多核服务器上，则不得不启动并且管理许多不同的过程。

这一点Vert.x的工作有所不同，每个Vertx实例不是维护的一个Event Loop，而是维护的多个。默认情况下，我们会根据机器上可用的核数量来选择Event Loop的数量，而这个（设置）可以被覆盖。

与Node.js不同，这意味着单个Vertx进程可以跨服务器扩展。

我们将这种模式称为多反应堆模式【Multi-Reactor】，将它和单线程反应堆模式进行区分。

_注意：即使一个Vertx实例维护了多个Event Loop，任何特定的处理器永远不会同时执行，大部分情况下（除开_[_Worker Verticle_](http://vertx.io/docs/vertx-core/java/#worker_verticles)之_外）它们总是在完全相同的Event Loop中被调用。_

### 黄金法则——不要阻塞Event Loop

我们已经知道，Vert.x的API都是非阻塞式的并且不会阻塞Event Loop，但是如果您在自己的处理器中阻塞了Event Loop，那么这不是很有帮助。

如果这样做，该Event Loop在被阻塞时就不能做任何事情。如果您阻塞了Vertx实例中的所有Event Loop，那么您的应用就会完全停止！

所以不要这样做！**您被警告了哦**

这些阻塞做法包括：

* `Thead.sleep()`
* 等待一个锁
* 等待一个互斥量或监视器【Mutex & Monitor】（参考：Synchronized Section）
* 执行一个长时间数据库操作并等待其结果
* 执行一个很占可用时间的复杂运算
* 在循环【Loop】中Spinning（？怎么翻译）

如果上述任何一种情况都阻止了Event Loop做任何事并占用了**大量可用时间**，那您（做了淘气的事）立即进入淘气的步骤，等待进一步指示。

所以，什么是**大量可用时间**？

您要等多久？它取决于您的应用程序和所需的并发数量。

如果您有一个单独的Event Loop，而且您希望每秒处理10000个HTTP请求，很明显的是每一个请求处理时间不可以超过0.1毫秒，所以您不能阻塞任何过多（大于0.1毫秒）的时间。

**这个数学题并不难，将留给读者作为练习。**

如果您的应用程序没有响应，可能是一个迹象，表明您在某个地方阻塞了Event Loop。为了帮助您诊断类似问题，若Vert.x检测到Event Loop有一段时间没有响应，将会自动记录这种警告。若您在日志中看到类似警告，那么您需要进行调查。

```
Thread vertx-eventloop-thread-3 has been blocked for 20458 ms
```

Vert.x还将提供堆栈跟踪，以精确定位发生阻塞的位置。

如果想关闭这些警告或更改设置，您可以在创建Vertx对象之前在VertxOptions中完成此操作。

### 运行阻塞式代码

在一个完美的世界中，不存在战争和饥饿，所有的API都将使用异步方式编写，兔兔和小羊羔将会在阳光明媚的绿色草地上手牵手地跳舞。

**但是……真实世界并非如此（您最近看到过这个消息吗？）**

事实是，很多，也非所有的库，特别是在JVM生态系统中有很多同步API，这些API中许多方法都是阻塞式的。一个很好的例子就是JDBC API——它本质上是同步的，无论多么努力地去尝试，Vert.x都不能像魔法小精灵撒尘变法一样将它转换成异步API。

我们不会将所有的内容重写成异步方式，所以我们为您提供一种在Vert.x应用中安全使用"传统"阻塞API的方法。

如之前讨论，您不能在Event Loop中直接调用阻塞式操作，因为这样做会阻止Event Loop执行其他有用的任务。那么您能怎么做？

可以通过调用[executeBlocking](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#executeBlocking-io.vertx.core.Handler-boolean-io.vertx.core.Handler-)方法来指定阻塞式代码的执行和阻塞式代码执行过后结果处理器的异步回调。

```java
vertx.executeBlocking(future -> {
  // Call some blocking API that takes a significant amount of time to return
  // 调用一些需要耗费大量可用时间返回结果的阻塞式API
  String result = someAPI.blockingMethod("hello");
  future.complete(result);
}, res -> {
  System.out.println("The result is: " + res.result());
});
```

默认情况，如果executeBlocking在同一个上下文环境中（如：同一个Verticle实例）被调用了多次，那么这些不同的executeBlocking代码块会顺序执行（一个接一个）。

若您不需要关心您调用[executeBlocking](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#executeBlocking-io.vertx.core.Handler-boolean-io.vertx.core.Handler-)的顺序，可以将`ordered`参数的值设为false。这样任何executeBlocking都会在一个worker Pool \(1\)中并行执行。

另外一种运行阻塞式代码的方法是使用[Worker Verticle](http://vertx.io/docs/vertx-core/java/#worker_verticles)。

一个Worker Verticle始终会使用worker Pool中的某个线程来执行。

默认的阻塞式代码会在Vert.x的blocking code pool中执行，通过[setWorkerPoolSize](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html#setWorkerPoolSize-int-)配置。

若有不同的目的，可以创建额外的pool：

```java
WorkerExecutor executor = vertx.createSharedWorkerExecutor("my-worker-pool");
executor.executeBlocking(future -> {
  // Call some blocking API that takes a significant amount of time to return
  // 调用一些需要耗费大量可用时间返回结果的阻塞式API
  String result = someAPI.blockingMethod("hello");
  future.complete(result);
}, res -> {
  System.out.println("The result is: " + res.result());
});
```

这个`worker executor` \(2\)在不需要的时候必须被关闭：

```java
executor.close();
```

当使用同一个名字创建了许多worker时，它们将共享同一个pool，所有的worker executor调用了`closed()`被关闭过后，这个worker pool会被销毁。

在一个Verticle中创建了一个`executor`时，Verticle实例被销毁的时候Vert.x将会自动关闭这个`executor`。

worker executor可以在创建的时候配置：

```java
int poolSize = 10;

// 2 minutes
// 2分钟
long maxExecuteTime = 120000;

WorkerExecutor executor = vertx.createSharedWorkerExecutor("my-worker-pool", poolSize, maxExecuteTime);
```

_注意：这个配置信息在worker pool创建的时候设置。_

### 异步协调

多个操作调用结果的协调是由Vert.x中的[futures](http://vertx.io/docs/apidocs/io/vertx/core/Future.html)来完成。它支持并发合并（并行执行多个异步调用）和顺序合并（依次执行异步调用）。

#### 并发合并【Concurrent Composition】

**1.all**

[CompositeFuture.all](http://vertx.io/docs/apidocs/io/vertx/core/CompositeFuture.html#all-io.vertx.core.Future-io.vertx.core.Future-)需要多个future参数（最多6个），当所有的future都成功完成时返回_succeeded_，如果有一个future执行失败则返回_failed_：

```java
Future<HttpServer> httpServerFuture = Future.future();
httpServer.listen(httpServerFuture.completer());

Future<NetServer> netServerFuture = Future.future();
netServer.listen(netServerFuture.completer());

CompositeFuture.all(httpServerFuture, netServerFuture).setHandler(ar -> {
  if (ar.succeeded()) {
    // All servers started
    // 所有服务器启动完成
  } else {
    // At least one server failed
    // 有一个服务器启动失败
  }
});
```

这些操作同时运行，所有返回结果合并完成时，附在返回future上的处理器（[Handler](http://vertx.io/docs/apidocs/io/vertx/core/Handler.html)）会被调用。当一个操作失败（传入一个future被标记成failure），则返回的future会被标记成failed。当所有的操作都成功时，返回的future被标记成success。

您可以传入一个future的列表（默认为空）：

```java
CompositeFuture.all(Arrays.asList(future1, future2, future3));
```

**2.any**

不同于`all`的合并会等待所有的future要么全成功（或其中一个失败），`any`的合并会等待第一个成功的future。[CompositeFuture.any](http://vertx.io/docs/apidocs/io/vertx/core/CompositeFuture.html#any-io.vertx.core.Future-io.vertx.core.Future-)需要多个future参数（最多6个），当任何一个future成功完成则返回_succeeded_，所有的future都失败则返回_failed_：

```java
CompositeFuture.any(future1, future2).setHandler(ar -> {
  if (ar.succeeded()) {
    // At least one is succeeded
    // 至少一个成功
  } else {
    // All failed
    // 所有的都失败
  }
});
```

它也可使用future列表传参：

```java
CompositeFuture.any(Arrays.asList(f1, f2, f3));
```

**3.join**

`join`的合并会等待所有的future完成，不论是一个成功还是一个失败都可。

[CompositeFuture.join](http://vertx.io/docs/apidocs/io/vertx/core/CompositeFuture.html#join-io.vertx.core.Future-io.vertx.core.Future-)需要多个future参数（最多6个），当所有的future都成功时返回一个_succeeded_的future，当所有的future都完成时，至少有一个future失败则返回_failed_。

```java
CompositeFuture.join(future1, future2, future3).setHandler(ar -> {
  if (ar.succeeded()) {
    // All succeeded
    // 所有都成功
  } else {
    // All completed and at least one failed
    // 至少一个失败
  }
});
```

它也可使用future列表传参：

```java
CompositeFuture.join(Arrays.asList(future1, future2, future3));
```

#### 顺序合并【Sequential Composition】

**1.compose**

和`all`以及`any`实现的并发合并不同，[compose](http://vertx.io/docs/apidocs/io/vertx/core/Future.html#compose-io.vertx.core.Handler-io.vertx.core.Future-)用于链式化【chaining】future（顺序合并）。

```java
FileSystem fs = vertx.fileSystem();
Future<Void> startFuture = Future.future();

Future<Void> fut1 = Future.future();
fs.createFile("/foo", fut1.completer());

fut1.compose(v -> {
  // When the file is created (fut1), execute this:
  // fut1中文件创建完成后执行
  Future<Void> fut2 = Future.future();
  fs.writeFile("/foo", Buffer.buffer(), fut2.completer());
  return fut2;
}).compose(v -> {
          // When the file is written (fut2), execute this:
          // fut2文件写入完成后执行
          fs.move("/foo", "/bar", startFuture.completer());
        },
        // mark startFuture it as failed if any step fails.
        // 如果任何一步失败，将startFuture标记成failed
        startFuture);
```

这里例子中，有三个操作被链式化：

1. 一个文件被创建（`fut1`）
2. 一些东西被写入到文件（`fut2`）
3. 文件被移走（`startFuture`）

如果这三个步骤全部成功，则最终的future（`startFuture`）返回_succeeded_，然而任何一步失败，则最终future返回_failed_。

使用例子：

* [compose](http://vertx.io/docs/apidocs/io/vertx/core/Future.html#compose-io.vertx.core.Handler-io.vertx.core.Future-)：当前future完成时，执行返回future的函数。当返回的future完成时，它会完成该合并。
* [compose](http://vertx.io/docs/apidocs/io/vertx/core/Future.html#compose-io.vertx.core.Handler-io.vertx.core.Future-)：当前future完成时，执行完成下一个future的处理器。

在第二个例子中，处理器（[Handler](http://vertx.io/docs/apidocs/io/vertx/core/Handler.html)）应该完成下一个（`next`）future过后来报告成功或者失败。

您可以使用[completer](http://vertx.io/docs/apidocs/io/vertx/core/Future.html#completer--)来完成一个带操作结果（自定义）或失败的future，它避免使用传统方式编写：如果成功则完成future，否则就失败【if success then complete the future else fail the future】。

### Verticles

标准环境的Vert.x提供了一个简单的、可扩展的、类似Actor-Model的部署模型和并发模型，您可以按自己的方式保存和编写。

**这个模型是可选的，如果您不想这样做，Vert.x不强制您用这种方式创建您的应用程序。**

这个模型不能说是严格的Actor-Model的实现，但它确实有相似之处，特别是在并发、扩展和部署等方面。

要使用该模型，您需要将您的代码组织成**verticle**的集合。

Verticle是由Vert.x部署和运行的代码块，默认情况一个Vert.x实例维护了N（默认：N = 核 x 2）个Event Loop线程。Verticle实例可使用任意Vert.x支持的计算机语言编写，一个简单的应用程序也可以包含多种语言编写的Verticle。

您可以将Verticle想成[Actor Model](https://en.wikipedia.org/wiki/Actor_model)中的Actor。

一个应用程序通常是由同一时间运行在同一个Vert.x实例中的许多Verticle实例组合而成。不同的Verticle实例可通过发送消息给event bus来相互通信。

#### 编写Verticle

Verticle类必须实现[Verticle](http://vertx.io/docs/apidocs/io/vertx/core/Verticle.html)接口。

如果您喜欢可以直接实现该接口，但是通常直接从抽象类[AbstractVerticle](http://vertx.io/docs/apidocs/io/vertx/core/AbstractVerticle.html)继承更简单。

这儿有一个例子：

```java
public class MyVerticle extends AbstractVerticle {

  // Called when verticle is deployed
  // Verticle部署时调用
  public void start() {
  }

  // Optional - called when verticle is undeployed
  // 可选 - Verticle撤销时调用
  public void stop() {
  }

}
```

通常您需要像上边例子一样重写start方法。

当Vert.x部署一个Verticle时，它的start方法将被调用，这个方法执行完成后Verticle会是started（的状态）。

您同样可以重写stop方法，当Vert.x撤销一个Verticle时它会被调用，这个方法执行完成后Verticle会是stopped（的状态）。

#### Verticle异步启动和停止

有些时候您的Verticle启动会耗费一些时间，您想要在这个过程做一些事，并且您做的这些事并不想等到Verticle部署完成过后再发生。如：您想在start方法中部署其他的Verticle。

您不能在您的start方法中阻塞等待其他的Verticle部署完成，这样做会破坏[黄金法则](http://vertx.io/docs/vertx-core/java/#golden_rule)。

所以您要怎么做？

您可以实现异步版本的start方法来做这个事。这个版本的方法需要一个Future作参数，方法执行完时，Verticle实例并没有部署好（状态不是deployed）。

稍后，您完成了所有您需要做的事（如：启动其他Verticle），您可以调用Future的complete（或fails）方法来示意您完成了。

这儿有一个例子：

```java
public class MyVerticle extends AbstractVerticle {

  public void start(Future<Void> startFuture) {
    // Now deploy some other verticle:
    // 现在部署其他的一些verticle
    vertx.deployVerticle("com.foo.OtherVerticle", res -> {
      if (res.succeeded()) {
        startFuture.complete();
      } else {
        startFuture.fail(res.cause());
      }
    });
  }
}
```

同样的，这儿也有一个异步版本的stop方法，如果您想做一些耗时的Verticle清理工作，您可以使用它。

```java
public class MyVerticle extends AbstractVerticle {

  public void start() {
    // Do something
    // 做一些事
  }

  public void stop(Future<Void> stopFuture) {
    obj.doSomethingThatTakesTime(res -> {
      if (res.succeeded()) {
        stopFuture.complete();
      } else {
        stopFuture.fail();
      }
    });
  }
}
```

_注意：您不需要在一个Verticle的stop方法中手工去撤销启动时部署的子Verticle，当父Verticle在撤销时Vert.x会自动撤销任何子Verticle。_

#### Verticle类型

这儿有三种不同类型的Verticle：

* **Stardand Verticles**：这是常用的一类Verticle——它们会被一个Event Loop线程执行，稍后的章节我们会讨论更多。
* **Worker Verticles**：这类Verticle会使用Worker Pool中的线程，一个实例绝对不会被多个线程同时执行。
* **Multi-threaded worker verticles**：这类Verticle也会使用Worker Pool中的线程，一个实例可以由多个线程同时执行。

#### Standard Verticles

当Standard Verticle被创建时，它会被分派给一个Event Loop线程，并在这个Event Loop中执行它的start方法。当您在一个Event Loop调用了Core API中处理器上任意其他方法时，Vert.x将保证这些处理器在调用时在相同的Event Loop中执行。

这意味着我们可以保证您的Verticle实例中所有的代码都是在相同Event Loop中执行（只要您不创建自己的线程并调用它！）

同样意味着您可以将您的应用中的所有代码用单线程方式编写，让Vert.x去担心线程和扩展问题。（您）不再担心同步和不稳定，当多线程应用开发使用传统方式手工回滚时，这种避免其他竞争条件和死锁（的方式）会更流行。

#### Worker Verticles

一个Worker Verticle和Standard Verticle很像，但它并不是由一个Event Loop来执行，而是由Vert.x中的Worker Pool中的线程执行。

Worker Verticle被设计来调用阻塞式代码，但它不会阻塞任何Event Loop。

如果您不想使用Worker Verticle来运行阻塞式代码，您还可以在一个Event Loop中直接使用[内联阻塞式代码](http://vertx.io/docs/vertx-core/java/#blocking_code)（前文中executeBlocking）。

若您想要将Verticle部署成一个Worker Verticle，用[setWorker](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setWorker-boolean-)来做：

```java
DeploymentOptions options = new DeploymentOptions().setWorker(true);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

Worker Verticle实例绝对不会在Vert.x中被多个线程同时执行，但它可以在不同时间由不同线程执行。

**Multi-threaded worker verticles**

一个multi-threaded worker verticle近似于普通的Worker Verticle，但是它可以由不同的线程同时执行。

*警告：Multi-threaded worker verticle是一个高级功能，大部分应用程序不会需要它。由于在这些Verticle中的并发性，您必须非常小心和使用Java技术中多线程编程方式保持状态一致性。*

#### 编程方式部署Verticle

您可以指定一个Verticle名称或传入您已经创建好的Verticle实例，使用任意一个[deployVerticle](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#deployVerticle-io.vertx.core.Verticle-)方法来部署Verticle。

*注意：部署Verticle仅限Java语言*

```java
Verticle myVerticle = new MyVerticle();
vertx.deployVerticle(myVerticle);
```

您同样可以指定Verticle的名称来部署它。

这个Verticle的名称会用于查找实例化Verticle的特定[VerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/spi/VerticleFactory.html)。

不同的Verticle Factory会对实例化不同语言的Verticle以及其他原因如加载服务、运行时从Maven中获取Verticle等——合法可用。

这允许您部署用任何Vert.x支持的语言编写的Verticle实例。

这儿有一个部署不同类型Verticle的例子：

```java
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle");

// Deploy a JavaScript verticle
// 部署JavaScript的Verticle
vertx.deployVerticle("verticles/myverticle.js");

// Deploy a Ruby verticle verticle
// 部署Ruby的Verticle
vertx.deployVerticle("verticles/my_verticle.rb");
```

#### Verticle名称到Factory的映射规则

若使用名称部署Verticle，这个名称用于选择一个将要实例化Verticle的Factory。

Verticle名称可以有一个前缀——使用字符串紧跟着一个冒号，它用于查找存在的Factory，参考例子。

```
js:foo.js // 使用JavaScript的Factory
groovy:com.mycompany.SomeGroovyCompiledVerticle // 用Groovy的Factory
service:com.mycompany:myorderservice // 用Service的Factory
```

如果不指定前缀，Vert.x将根据提供名字后缀来查找对应Factory，如：

```
foo.js // 将使用JavaScript的Factory
SomeScript.groovy // 将使用Groovy的Factory
```

若前缀后缀都没指定，Vert.x将假定这个名字是一个Java限定类全名（FQCN）然后尝试实例化它。

#### 如何定位Verticle Factory？

大部分Verticle Factory会在Vert.x启动时注册并且从类路径（CLASSPATH）中加载。

您同样可以使用编程的方式去注册或注销Verticle Factory：[registerVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#registerVerticleFactory-io.vertx.core.spi.VerticleFactory-)和[unregisterVerticleFactory](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#unregisterVerticleFactory-io.vertx.core.spi.VerticleFactory-)

#### 等待部署完成

Verticle的部署是异步方式，可能在deploy方法调用返回后一段时间才会完成部署。

如果您想要在部署完成时发出通知则可以指定一个Completion Handler。

```java
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", res -> {
  if (res.succeeded()) {
    System.out.println("Deployment id is: " + res.result());
  } else {
    System.out.println("Deployment failed!");
  }
});
```

如果部署成功，这个Completion Handler的结果（result）中将会传入一个包含了部署id的字符串。

这个部署ID可以在之后您想要撤销它时使用。

#### 撤销Verticle

部署（好的Verticle）可以使用[undeploy](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#undeploy-java.lang.String-)被撤销。

撤销也是异步方式，因此若您想要在撤销完成过后收到通知则可以指定另一个Completion Handler。

```java
vertx.undeploy(deploymentID, res -> {
  if (res.succeeded()) {
    System.out.println("Undeployed ok");
  } else {
    System.out.println("Undeploy failed!");
  }
});
```

#### 设置Verticle实例数

当使用名称部署一个Verticle时，您可以指定您想要部署的Verticle实例的数量。

```java
DeploymentOptions options = new DeploymentOptions().setInstances(16);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

若跨多核简化水平扩展时这个功能很有用。如，您的计算机上有一个包含Web服务器的Verticle需要部署以及跨多核，因此您需要部署多个实例来利用所有的（服务器）核。

#### 传入配置给Verticle

一个JSON格式的配置可在部署时传给Verticle

```java
JsonObject config = new JsonObject().put("name", "tim").put("directory", "/blah");
DeploymentOptions options = new DeploymentOptions().setConfig(config);
vertx.deployVerticle("com.mycompany.MyOrderProcessorVerticle", options);
```

这个配置之后通过[Context](http://vertx.io/docs/apidocs/io/vertx/core/Context.html)对象或直接使用[config](http://vertx.io/docs/apidocs/io/vertx/core/AbstractVerticle.html#config--)方法访问【Available】。

这个配置会返回一个JSON对象，因此您可以用下边代码读取之中数据：

```java
System.out.println("Configuration: " + config().getString("name"));
```

#### 访问Verticle环境变量

环境变量和系统属直接通过Java API访问：

```java
System.getProperty("prop");
System.getenv("HOME");
```

#### Verticle隔离组【Isolation Groups】

默认情况，Vert.x有一个水平类路径【flat classpath】，当Vert.x部署Verticle时它会调用当前类加载器来做——它不会创建一个新的（类加载器），大多数情况下，这是最简单、最清晰和最干净的事。

但是在某些情况下，您可能需要部署一个Verticle，它包含的类要与应用程序中其他类隔离开来。

可能是这种情况，例如：您想要在一个Vert.x实例中部署两个同名不同类的Verticle，或者在同一个jar库文件中您有两个不同版本的Verticle。

当使用隔离组时，您需要用[setIsolatedClassed](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setIsolatedClasses-java.util.List-)方法，并提供一个您想隔离的类名列表给它——（列表）项可以是一个Java限定类全名如`com.mycompany.myproject.engine.MyClass`，也可以是包含通配符的可匹配某个包或子包的任何类。例如`com.mycompany.myproject.*`将会匹配所有`com.mycompany.myproject`包或任意子包中的任意类名。

请注意仅仅只有匹配的类会被隔离——其他任意类会被当前类加载器加载。

若您想要加载的类和资源不存在于主类路径，您可使用[setExtraClasspath](http://vertx.io/docs/apidocs/io/vertx/core/DeploymentOptions.html#setExtraClasspath-java.util.List-)将额外的类路径添加到这里。

*警告：谨慎使用此功能，类加载器可能是一个蠕虫病毒，除其他事情外它会使调试变得困难。*

以下是使用隔离组隔离Verticle的部署例子：

```java
DeploymentOptions options = new DeploymentOptions().setIsolationGroup("mygroup");
options.setIsolatedClasses(Arrays.asList("com.mycompany.myverticle.*",
                   "com.mycompany.somepkg.SomeClass", "org.somelibrary.*"));
vertx.deployVerticle("com.mycompany.myverticle.VerticleClass", options);
```

#### 高可用性【High Availability】

Verticle可以启用高可用方式（HA）部署，在上下文环境中，当其中一个部署在Vert.x实例中的Verticle突然死掉，这个Verticle可以在集群环境中的另一个Vert.x实例中重新部署。

若要启用高可用方式运行一个Verticle，仅需要追加`-ha`参数：

```
	vertx run my-verticle.js -ha
```

当启用高可用方式时，不需要追加`-cluster`参数。

关于高可用的功能和配置可参考[高可用和故障切换](http://vertx.io/docs/vertx-core/java/#_high_availability_and_fail_over)

#### 从命令行运行Verticle

您可以在Maven或Gradle项目中以正常方式添加Vert.x Core到项目依赖节点，从哪里直接使用Vert.x。

但是，您也可以从命令行直接运行Vert.x的Verticle。

为此，您需要下载并安装Vert.x的发行版【distribution】，并且将安装的`bin`目录添加到您的`PATH`环境变量中，还要确保您的`PATH`中设置了Java 8的JDK环境。

*注意：JDK需要支持Java代码的快速编译【Fly compilcation】。*

现在您可以使用`vertx run`命令运行Verticle了，这儿是一些例子：

```
# Run a JavaScript verticle
# 运行JavaScript的Verticle
vertx run my_verticle.js

# Run a Ruby verticle
# 运行Ruby的Verticle
vertx run a_n_other_verticle.rb

# Run a Groovy script verticle, clustered
# 使用集群模式运行Groovy的Verticle
vertx run FooVerticle.groovy -cluster
```

您甚至可以不必编译Java源代码，直接运行它：

```
vertx run SomeJavaSourceFile.java
```

Vert.x将在运行它之前对Java源代码文件执行快速编译，这对于快速原型制作很有用并非常适用于演示——没有必要首先设置一个Maven或Gradle来构建！

有关在命令行执行`vertx`可用的各种选项完整信息，可以直接在命令行键入`vertx`（查看）。

#### 导致Vert.x退出

所有Vert.x实例维护的线程不是守护线程，所以它们将会阻止JVM退出。

如果您正在嵌入Vert.x并且完成了它，您可以调用[close](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#close--)方法关闭它。

这将关闭所有内部线程池并关闭其他资源，而且将允许JVM退出。

#### Context对象

当Vert.x传递一个事件给处理器或者调用Verticle的`start`或`stop`方法时，它的执行会关联一个`Context`对象。通常一个context又是一个event-loop context并且绑定到一个特定的event-loop线程，所以执行该context总是在同一个event loop线程中；同样Worker Vertcle以及运行的内联阻塞式代码中，一个worker context将会绑定其执行关联到一个特定的worker pool的线程中。

您可用[getOrCreateContext](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#getOrCreateContext--)方法获取context：

```java
Context context = vertx.getOrCreateContext();
```

若已经有一个context和当前线程关联，那么它直接重用这个context对象，如果没有则它会创建一个新的。您可以测试获取的context的类型：

```java
Context context = vertx.getOrCreateContext();
if (context.isEventLoopContext()) {
  System.out.println("Context attached to Event Loop");
} else if (context.isWorkerContext()) {
  System.out.println("Context attached to Worker Thread");
} else if (context.isMultiThreadedWorkerContext()) {
  System.out.println("Context attached to Worker Thread - multi threaded worker");
} else if (! Context.isOnVertxThread()) {
  System.out.println("Context not attached to a thread managed by vert.x");
}
```

当您获取了这个context对象，您就可以在context中异步执行代码了。换句话说，您提交一个任务（和之后的任务）最终将会在同一个context中运行：

```java
vertx.getOrCreateContext().runOnContext( (v) -> {
  System.out.println("This will be executed asynchronously in the same context");
});
```

若同一个context中运行了多个处理器，它们也许想要共享数据，这个context对象提供了在context中存储和读取数据（实现）共享的方法。例如：它允许您将数据传递到[runOnContext](http://vertx.io/docs/apidocs/io/vertx/core/Context.html#runOnContext-io.vertx.core.Handler-)运行的某些操作中。

```java
final Context context = vertx.getOrCreateContext();
context.put("data", "hello");
context.runOnContext((v) -> {
  String hello = context.get("data");
});
```

这个context对象还可以让您使用[config](http://vertx.io/docs/apidocs/io/vertx/core/Context.html#config--)方法访问Verticle的配置信息，查看传入配置给Verticle章节了解更多配置信息。

#### 执行周期性/延迟性操作

Vert.x中，想要延迟之后执行或定期执行操作很常见。

在Standard Verticle中您不能让线程休眠以引入延迟，因为它会阻塞Event Loop线程。

取而代之是使用Vert.x定时器，定时器可以是一次性【One-shot】或定期的【periodic】，我们将讨论二者。

**一次性计时器【One-shot】**

一个一次性计时器会在一定延迟后调用一个Event Handler，以毫秒为单位。

您可用[setTimer](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setTimer-long-io.vertx.core.Handler-)方法传递延迟和一个处理器来设置计时器的触发。

```java
long timerID = vertx.setTimer(1000, id -> {
  System.out.println("And one second later this is printed");
});

System.out.println("First this is printed");
```

返回值是一个唯一的计时器id，该id可用于之后取消该计时器，这个计时器id会传入给处理器。

**周期性计时器【Periodic】**

您同样可以使用[setPeriodic](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setPeriodic-long-io.vertx.core.Handler-)设置一个周期性触发的计时器。

这儿将有一个等于该时间的初始化延迟。

setPeriodic的返回值也是一个唯一的计时器id，若之后该计时器需要取消则使用该id。

传给这个计时器中处理器的参数也是这个唯一的计时器id。

请记住这个计时器将会定期触发，如果您的定期（任务）执行【treatment】将会花费大量时间，您的计时器事件能持续执行或最坏的情况：重叠。

这种情况，您应考虑使用[setTimer](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#setTimer-long-io.vertx.core.Handler-)，一旦任务执行完成您可设置下一个计时器。

```java
long timerID = vertx.setPeriodic(1000, id -> {
  System.out.println("And every second this is printed");
});

System.out.println("First this is printed");
```

**取消计时器**

指定一个计时器id并调用[cancelTimer](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#cancelTimer-long-)来取消一个周期性计时器。如：

```java
vertx.cancelTimer(timerID);
```

**Verticle中自动清除**

如果您在Verticle中创建了计时器，当这个Verticle被撤销时这个计时器会被自动关闭。

#### Verticle工作线程池

Verticle使用Vert.x中的工作线程池【Worker Pool】来执行阻塞式行为——如：[executeBlocking](http://vertx.io/docs/apidocs/io/vertx/core/Context.html#executeBlocking-io.vertx.core.Handler-boolean-io.vertx.core.Handler-)或Worker Verticle。

还可以在部署配置项中指定不同的工作线程池：

```java
vertx.deployVerticle("the-verticle", new DeploymentOptions().setWorkerPoolName("the-specific-pool"));
```

### Event Bus

Event Bus是Vert.x中的神经系统【Nervous System】。

每一个Vert.x实例都有一个单独的Event Bus实例，可通过方法[eventBus](http://vertx.io/docs/apidocs/io/vertx/core/Vertx.html#eventBus--)来获得。

Event Bus您的应用中的不同部分相互通信，无论它们使用哪一种语言实现，以及它们是否在同一个Vert.x实例中，或在不同的Vert.x实例中。

它甚至可以桥接允许在浏览器中运行的客户端JavaScript在相同的Event Bus上相互通信。

Event Bus可形成跨越多个服务器节点和多个浏览器的分布式对等消息系统。

Event Bus支持发布/订阅、点对点、请求/响应的消息通信（模式）。

Event Bus的API很简单，它主要涉及注册处理器、撤销处理器和发送和发布消息。

首先看些理论：

#### 理论

**寻址【Addressing】**

消息在Event Bus上会发送到一个地址【Address】。

同任何花哨的寻址方式相比Vert.x（寻址）并不麻烦，Vert.x中的地址是一个简单的字符串，任意字符串都合法。然后，使用某种模式仍然是明智之举，如：使用据点来划分名空间。

一些合法的地址形如：europe.news.feed1、acme.games.pacman, sausages和X。

**处理器【Handlers】**

消息在处理器中被接收，您可以在一个地址上注册一个处理器。

同一个地址可以注册许多不同的处理器，一个处理器也可以在许多不同的地址上注册。

**发布/订阅消息【Publish/Subscribe Messaging】**

Event Bus支持发布消息功能。

消息将被发布到一个地址中，发布意味着将详细传递给所有注册在该地址上的处理器。

这和发布/订阅模式很类似。

**点对点和请求-响应消息**

Event Bus也支持点对点消息。

消息将被发送到一个地址中，Vert.x将会把消息分发【route】到某个注册在该地址上的处理器。

若这个地址上有不止一个注册过的处理器，它将使用不严格的轮询算法选择其中一个。

通过点对点的消息传递，可在消息发送的时候指定可选的Reply Handler。

当接收者收到消息并且已经被处理时，它可以选择性决定回复该消息，若选择回复则Reply Handler将会被调用。

当发送者收到回复消息时，它也可以回复，这个过程可以不断重复，它允许在两个不同的Verticle之间设置一个对话框。

这就是一种称为**请求-响应**模式的消息模式。

**尽力服务传输【Best-effort delivery】**

Vert.x会尽它最大努力去传递消息，并且保证消息不会出现丢失，这种称为**尽力服务**传输。

但是，在Event Bus中全部或部分（位置）发生故障，则可能会丢失消息。

若您的应用关心丢失的消息，您应该编写具有幂等性的处理器，并且您的发送者可以在恢复后重试。

**消息类型【Types of messages】**

标准Vert.x允许任何基本/简单类型、String或[buffers](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html)作为消息发送。

不过在Vert.x中通常以[JSON](http://json.org/)格式作为发送消息的惯例和常用实践。

对于Vert.x支持的所有语言，JSON非常容易创建、读取和解析，因此它已经成为了Vert.x中的通用语【lingua franca】。

但是若您不想用它，并不强制您使用JSON。

Event Bus非常灵活并且支持在Event Bus中发送任意对象，您可以针对您想要发送的对象自定义一个[codec](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageCodec.html)来实现。

#### Event Bus API

让我们跳到API

**获取Event Bus**

您可以使用下边代码获取Event Bus的一个引用：

```java
EventBus eb = vertx.eventBus();
```

在一个Vert.x实例中它是单例的。

**注册处理器**

最简单的注册处理器的方式是使用[consumer](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#consumer-java.lang.String-io.vertx.core.Handler-)，这儿有个例子：

```java
EventBus eb = vertx.eventBus();

eb.consumer("news.uk.sport", message -> {
  System.out.println("I have received a message: " + message.body());
});
```

当一个消息达到您的处理器，它将被调用，传递[消息](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html)。

consumer()调用过后的返回对象是一个[MessageConsumer](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageConsumer.html)的实例。

该对象随后可用于撤销处理器、或将处理器用作流。

或者、您可以不设置处理器而使用[consumer](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#consumer-java.lang.String-io.vertx.core.Handler-)返回一个MessageConsumer，之后再来设置处理器。如：

```java
EventBus eb = vertx.eventBus();

MessageConsumer<String> consumer = eb.consumer("news.uk.sport");
consumer.handler(message -> {
  System.out.println("I have received a message: " + message.body());
});
```

在集群的Event Bus上注册处理器时，注册会花费一些时间才能到达集群中的所有节点。

若您希望在完成后收到通知，您可以在MessageConsumer对象上注册一个[completion handler](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageConsumer.html#completionHandler-io.vertx.core.Handler-)。

```java
consumer.completionHandler(res -> {
  if (res.succeeded()) {
    System.out.println("The handler registration has reached all nodes");
  } else {
    System.out.println("Registration failed!");
  }
});
```

**撤销处理器**

要撤销处理器，请调用[unregister](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageConsumer.html#unregister--)。

若你位于一个集群的Event Bus中，撤销处理器同样会花费一些时间跨节点传播，若您想在完成后收到通知：

```java
consumer.unregister(res -> {
  if (res.succeeded()) {
    System.out.println("The handler un-registration has reached all nodes");
  } else {
    System.out.println("Un-registration failed!");
  }
});
```

**发布消息**

发布消息很简单，仅用[publish](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#publish-java.lang.String-java.lang.Object-)指定一个地址去发布

```java
eventBus.publish("news.uk.sport", "Yay! Someone kicked a ball");
```

这个消息将会传递给所有在地址news.uk.sport上注册过的处理器。

**发送消息**

发送消息将会把结果传递给在该地址注册的所有处理器中的一个来接收，这是点对点模式，处理器的选择使用不严格的轮询算法。

您可以使用[send](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#send-java.lang.String-java.lang.Object-)来发送消息：

```java
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball");
```

**设置消息头**

在Event Bus上发送的消息可包含头信息。

这可通过在发送或发布时提供的[DeliveryOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/DeliveryOptions.html)来指定。

```java
DeliveryOptions options = new DeliveryOptions();
options.addHeader("some-header", "some-value");
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball", options);
```

**消息顺序**

Vert.x将按照特定发送者发送消息的顺序来传递消息给特定处理器。

**消息对象**

您在Message Handler中接收到的一个对象就是一个[Message](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html)。

消息的[body](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html#body--)对应于发送或发布的对象。

消息的头信息在[headers](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html#headers--)中可用。

**确认消息/发送回复**

当使用[send](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#send-java.lang.String-java.lang.Object-)(发送消息）时Event Bus会尝试将详细传递到在Event Bus上注册过的[MessageConsumer](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageConsumer.html)中。

在某些情况下，发送者知道消费者何时收到消息并“处理”消息是有用的。

要确认消息已被处理，消费者可以通过调用[reply](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html#reply-java.lang.Object-)来回复消息。

当这种情况发生，它会将消息回发给发送者并且在回复中调用Reply Handler。

一个例子将使之更清楚：

接收者【Receiver】：

```java
MessageConsumer<String> consumer = eventBus.consumer("news.uk.sport");
consumer.handler(message -> {
  System.out.println("I have received a message: " + message.body());
  message.reply("how interesting!");
});
```

发送者【Sender】：

```java
eventBus.send("news.uk.sport", "Yay! Someone kicked a ball across a patch of grass", ar -> {
  if (ar.succeeded()) {
    System.out.println("Received reply: " + ar.result().body());
  }
});
```

这个回复可有用信息在消息体中。

什么是“处理”？实际上它是应用程序定义的，完全取决于消费者如何执行，不是Vert.x中的Event Bus本身知道或关心的东西，

一些例子：

* 一个简单的实现了一个返回当天时间服务的消息消费者会去确认回复的消息体中包含了当天时间信息。
* 一个实现了持久化队列的消息消费者，也许会用`true`确认消息已经成功持久化到存储中，或`false`表示（持久化）失败。
* 一个处理订单的消息消费者也许会用`true`确认这个订单已经成功处理，因此它可以从数据库中删除。

**带超时的发送**

当发送带有Reply Handler的消息时，可以在[DeliveryOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/DeliveryOptions.html)中指定一个超时时间。

如果恢复在这个时间只能没有收到，则Reply Handler将被调用失败。默认超时是30秒。

**发送故障**

消息发送可能会因为其他原因故障，包括：

* 没有和发送消息对应的处理器可用
* 接收者调用了[fail](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/Message.html#fail-int-java.lang.String-)显示声明故障

在所有情况Reply Handler将会针对特定的故障被调用。

**消息编解码器【Message Codecs】**

若您定义并注册了一个[消息编解码](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/MessageCodec.html)器，您可以在Event Bus中发送任意您想要的对象。

消息编解码器有一个名称，并且在发送或发布消息时在[DeliveryOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/DeliveryOptions.html)中指定。

```java
eventBus.registerCodec(myCodec);

DeliveryOptions options = new DeliveryOptions().setCodecName(myCodec.name());

eventBus.send("orders", new MyPOJO(), options);
```

若您总是希望将特定类型对象用于相同的编解码器，那么您可以为其注册默认编解码器，这样您就不需要在[DeliveryOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/DeliveryOptions.html)中指定特定编解码器。

```java
eventBus.registerDefaultCodec(MyPOJO.class, myCodec);

eventBus.send("orders", new MyPOJO());
```

您可以使用[unregisterCodec](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBus.html#unregisterCodec-java.lang.String-)撤销一个消息编解码器。

消息编解码器并不一定使用同一类型进行编码和解码。例如您可以编写一个编解码器允许MyPOJO类被编码发送，但是当消息发送给处理器后解码成MyOtherPOJO类。

**集群Event Bus**

Event Bus不仅仅存在于单个Vert.x实例中，通过您在网络上将不同的Vert.x实例集群在一起，它可以形成一个单一的、分布式的Event Bus。

**编程化集群**

若您用编程的方式创建Vert.x实例，则可以通过将Vert.x实例配置成集群来获取集群的Event Bus。

```java
VertxOptions options = new VertxOptions();
Vertx.clusteredVertx(options, res -> {
  if (res.succeeded()) {
    Vertx vertx = res.result();
    EventBus eventBus = vertx.eventBus();
    System.out.println("We now have a clustered event bus: " + eventBus);
  } else {
    System.out.println("Failed: " + res.cause());
  }
});
```

您应该确保您有一个[ClusterManager](http://vertx.io/docs/apidocs/io/vertx/core/spi/cluster/ClusterManager.html)实现类在您的类路径中，如默认的`HazelcastClusterManager`。

**通过命令行集群**

您可以用下边命令运行一个Vert.x集群

```
vertx run my-verticle.js -cluster
```

**Verticle中的自动清理**

若您在Verticle中注册了Event Bus的处理器，这些处理器在Verticle被撤销的时候会自动被注销。

### 配置Event Bus

Event Bus可以配置，当Event Bus运行在集群模式这是特别有用的。在引擎【hood】之下，Event Bus使用TCP连接发送和接收消息，因此[EventBusOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBusOptions.html)可以让您配置TCP连接的所有方面。由于Event Bus作为了服务器和客户端，这些配置近似于[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)和[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)。

```java
VertxOptions options = new VertxOptions()
    .setEventBusOptions(new EventBusOptions()
        .setSsl(true)
        .setKeyStoreOptions(new JksOptions().setPath("keystore.jks").setPassword("wibble"))
        .setTrustStoreOptions(new JksOptions().setPath("keystore.jks").setPassword("wibble"))
        .setClientAuth(ClientAuth.REQUIRED)
    );

Vertx.clusteredVertx(options, res -> {
  if (res.succeeded()) {
    Vertx vertx = res.result();
    EventBus eventBus = vertx.eventBus();
    System.out.println("We now have a clustered event bus: " + eventBus);
  } else {
    System.out.println("Failed: " + res.cause());
  }
});
```

上边代码段描述了如何在Event Bus中使用SSL连接替换纯的TCP连接。

*警告：要在集群模式下强制执行安全性，您必须将集群管理器【Cluster】配置成加密的或强制安全性（的方式）。参考集群管理器的文档获取更多细节。*

Event Bus的配置需要在所有集群节点中保持一致性。

[EventBusOptions](http://vertx.io/docs/apidocs/io/vertx/core/eventbus/EventBusOptions.html)还允许您指定Event Bus是否集群、主机信息和端口，您可使用[setClustered](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html#setClustered-boolean-)、[getClusterHost](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html#getClusterHost--)和[getClusterPort](http://vertx.io/docs/apidocs/io/vertx/core/VertxOptions.html#getClusterPort--)这样做。

在容器中使用时，您也可以配置公共主机和端口号：

```java
VertxOptions options = new VertxOptions()
    .setEventBusOptions(new EventBusOptions()
        .setClusterPublicHost("whatever")
        .setClusterPublicPort(1234)
    );

Vertx.clusteredVertx(options, res -> {
  if (res.succeeded()) {
    Vertx vertx = res.result();
    EventBus eventBus = vertx.eventBus();
    System.out.println("We now have a clustered event bus: " + eventBus);
  } else {
    System.out.println("Failed: " + res.cause());
  }
});
```

### JSON

和其他一些语言不同，Java没有对JSON的原生支持【first class support】，因此我们提供了两个类，以便在Vert.x应用中处理JSON更容易。

#### JSON对象

[JsonObject](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html)类用来描述JSON对象。

一个JSON对象基本上只是一个Map结构，它具有字符串的键，值可以是任意一种JSON支持的类型（string, number, boolean）。

JSON对象也支持null值。

**创建JSON对象**

可以使用默认构造函数创建空的JSON对象。

您可以从JSON格式的字符串创建一个JSON对象：

```java
String jsonString = "{\"foo\":\"bar\"}";
JsonObject object = new JsonObject(jsonString);
```

您可以从一个Map创建一个JSON对象：

```java
Map<String, Object> map = new HashMap<>();
map.put("foo", "bar");
map.put("xyz", 3);
JsonObject object = new JsonObject(map);
```

**将条目放入JSON对象**

使用[put](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html#put-java.lang.String-java.lang.Enum-)方法可以将值放入到JSON对象。

因为Vert.x支持Fluent的API，所以这个方法调用可以是链式化的。

```java
JsonObject object = new JsonObject();
object.put("foo", "bar").put("num", 123).put("mybool", true);
```

**从JSON对象获取值**

您可使用`getXXX`方法从JSON对象中获取值。例如：

```java
String val = jsonObject.getString("some-key");
int intVal = jsonObject.getInteger("some-other-key");
```

**JSON对象和Java对象间的映射**

您可以从Java对象的字段创建一个JSON对象，如下所示：

(官缺）

也可以实例化一个Java对象并从JSON对象填充其字段值。如下所示：

```java
request.bodyHandler(buff -> {
  JsonObject jsonObject = buff.toJsonObject();
  User javaObject = jsonObject.mapTo(User.class);
});
```

请注意上述映射方向都使用了Jackson的`ObjectMapper#convertValue()`来执行映射，字段和构造函数可见性的影响、有关跨对象引用的序列化完和反序列化等可参考Jackson的文档获取更多信息。

然而在最简单的情况下，所有Java类中字段都是public（或者有public的getter/setter）时，并且有一个public的默认构造函数（或不定义构造函数），`mapFrom`和`mapTo`都应该成功。

只要对象图是非循环的，引用对象通过to/from执行过滤性的序列化和反序列化时同样会作用于嵌套JSON对象。

**将JSON对象编码成String**

您可使用[encode](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonObject.html#encode--)将一个对象编码成字符串格式。

#### JSON数组

[JsonArray](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonArray.html)类用来描述JSON数组。

一个JSON数组是一系列值的有序集（string、number、boolean）。

JSON数组同样可以包含null值。

**创建JSON数组**

可以使用默认构造函数创建空的JSON数组。

您可以从JSON格式的字符串创建一个JSON数组：

```java
String jsonString = "[\"foo\",\"bar\"]";
JsonArray array = new JsonArray(jsonString);
```

**将条目添加JSON数组**

您可以使用[add](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonArray.html#add-java.lang.Enum-)方法添加条目到JSON数组中

```java
JsonArray array = new JsonArray();
array.add("foo").add(123).add(false);
```

**从JSON数组中获取值**

您可使用`getXXX`方法从JSON数组中获取值。例如：

```java
String val = array.getString(0);
Integer intVal = array.getInteger(1);
Boolean boolVal = array.getBoolean(2);
```

**将JSON数组编码成String**

您可使用[encode](http://vertx.io/docs/apidocs/io/vertx/core/json/JsonArray.html#encode--)将一个数组编码成字符串格式。

### Buffers

在Vert.x内部大部分数据使用Buffers的格式【Shuffled】

一个Buffer是可以读取或写入的0个或多个字节序列，并且根据需要可以自动扩容、将任意字节写入Buffer。您也许可以将Buffer想成智能字节数组。

#### 创建Buffer

可以使用一个静态方法[Buffer.buffer](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#buffer--)来创建Buffer。

Buffer可以从字符串或字节数组初始化，或者创建空的Buffer。

这儿有一些创建Buffer的例子：

创建一个空的Buffer：

```java
Buffer buff = Buffer.buffer();
```

从字符串创建一个Buffer，这个Buffer中的字符串必须可用UTF-8编码：

```java
Buffer buff = Buffer.buffer("some string");
```
从字符串创建一个BUffer，这个字符串可以用指定的编码方式编码，例如：

```java
Buffer buff = Buffer.buffer("some string", "UTF-16");
```

从字节数组byte[]创建Buffer：

```java
byte[] bytes = new byte[] {1, 3, 5};
Buffer buff = Buffer.buffer(bytes);
```

创建一个带有初始化尺寸的Buffer。若您知道您的Buffer会写入一定量的数据，您可以创建Buffer并指定它的尺寸。这使得这个Buffer初始化时分配了更多的内存，比数据写入时重新调整尺寸效率更高。

注意以这种方式创建的Buffer是空的，它也不会创建一个填满了0的Buffer。

```java
Buffer buff = Buffer.buffer(10000);
```

#### 写入Buffer

写入Buffer的方式有两种：追加和随机访问。任何一种情况下Buffer始终进行自动扩容，所以不可能在Buffer中遇到`IndexOutOfBoundsException`。

1. **追加到Buffer**

您可以使用`appendXXX`方法追加数据到Buffer，它存在各种不同数据类型的方法。

因为`appendXXX`方法的返回值就是Buffer自身，所以它可以链式化【Fluent】:

```java
Buffer buff = Buffer.buffer();

buff.appendInt(123).appendString("hello\n");

socket.write(buff);
```

2. **随机访问写Buffer**

您还可以指定一个索引值，通过`setXXX`方法写入数据到Buffer，它也存在各种不同数据类型的方法。所有的set方法都会将索引值作为第一个参数——这表示Buffer中开始写入数据的位置。

Buffer始终根据需要进行自动扩容：

```java
Buffer buff = Buffer.buffer();

buff.setInt(1000, 123);
buff.setString(0, "hello");
```

#### 从Buffer中读取

可使用`getXXX`方法从Buffer中读取数据，它存在各种不同数据类型的方法，这些方法的第一个参数是从哪里获取数据的索引（获取位置）。

```java
Buffer buff = Buffer.buffer();
for (int i = 0; i < buff.length(); i += 4) {
  System.out.println("int value at " + i + " is " + buff.getInt(i));
}
```

#### 使用无符号数据

可使用`getUnsignedXXX`，`appendUnsignedXXX`和`setUnsignedXXX`方法将无符号正数从Buffer中读取或追加/设置到Buffer。这对实现优化网络协议和最小化带宽消耗而实现的编解码器是很有用的。

下边例子中，值200倍设置到一个仅占用一个字节的固定位置：

```java
Buffer buff = Buffer.buffer(128);
int pos = 15;
buff.setUnsignedByte(pos, (short) 200);
System.out.println(buff.getUnsignedByte(pos));
```
控制台中显示'200'。

#### Buffer长度

可使用[length](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#length--)获取Buffer长度，Buffer的长度值是Buffer中包含字节（数据）的最大索引 + 1。

#### 拷贝Buffer

可使用[copy](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#copy--)创建一个Buffer的副本。

#### 分片Buffer

一个分片Buffer是基于原始Buffer的一个新的Buffer，如，它不会拷贝底层数据。使用[slice](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html#slice--)创建一个分片Buffer。

#### Buffer重用

将Buffer写入到一个Socket或其他类似位置后，Buffer就不可被重用了。

### 编写TCP服务器和客户端

Vert.x允许您很容易编写非阻塞的TCP客户端和服务器。

#### 创建一个TCP服务器

使用所有默认配置项，最简单地创建一个TCP服务器如下：

```java
NetServer server = vertx.createNetServer();
```

#### 配置一个TCP服务器

若您不想使用默认配置，可以在创建时通过传入一个[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)实例来配置服务器：

```java
NetServerOptions options = new NetServerOptions().setPort(4321);
NetServer server = vertx.createNetServer(options);
```

#### 启动服务监听【Server Listening】

要告诉服务器监听传入的请求，您可以使用其中一个[listen](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#listen--)方式。

在配置项中告诉服务器监听指定的主机和端口：

```java
NetServer server = vertx.createNetServer();
server.listen();
```

或在调用`listen`时指定主机和端口号，这样就忽略了配置项（中的主机和端口）：

```java
NetServer server = vertx.createNetServer();
server.listen(1234, "localhost");
```

默认主机名是`0.0.0.0`，它表示：监听所有可用地址；默认端口号是`0`，这也是一个特殊值，它告诉服务器本地没有使用的端口中随机选择一个并且使用它。

实际的绑定也是异步的，因此服务器也许并没有在调用listen返回时监听，而是在一段时间过后（才监听）。

若您希望在服务器实际监听时收到通知，您可以向listen提供一个处理器。例如：

```java
NetServer server = vertx.createNetServer();
server.listen(1234, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening!");
  } else {
    System.out.println("Failed to bind!");
  }
});
```

#### 监听随机端口

若设置监听端口为`0`，服务器将随机寻找一个没有使用的端口来监听。

若您想知道服务器实际监听的端口，可以调用[actualPort](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#actualPort--)方法：

```java
NetServer server = vertx.createNetServer();
server.listen(0, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening on actual port: " + server.actualPort());
  } else {
    System.out.println("Failed to bind!");
  }
});
```

#### 收到传入连接【Incoming Connection】的通知

若您想要在连接创建完时收到通知，则需要设置一个[connectHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#connectHandler-io.vertx.core.Handler-)：

```java
NetServer server = vertx.createNetServer();
server.connectHandler(socket -> {
  // Handle the connection in here
  // 在这里处理连接
});
```

当连接成功时，将会传入一个[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)实例给处理器，并调用它。

这是一个与实际连接类似的socket接口，它允许您读取和写入数据、以及执行各种其他操作如关闭socket。

#### 从Socket读取数据

您可以在Socket中设置一个[handler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#handler-io.vertx.core.Handler-)从Socket中读取数据。

每次当Socket接收到数据时，会传入一个[Buffer](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html)实例给处理器，并调用它。

```java
NetServer server = vertx.createNetServer();
server.connectHandler(socket -> {
  socket.handler(buffer -> {
    System.out.println("I received some bytes: " + buffer.length());
  });
});
```

#### 写数据到Socket

您可使用[write](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#write-io.vertx.core.buffer.Buffer-)方法写入数据到Socket：

```java
Buffer buffer = Buffer.buffer().appendFloat(12.34f).appendInt(123);
socket.write(buffer);

// Write a string in UTF-8 encoding
// 以UTF-8的编码方式写入一个字符串
socket.write("some data");

// Write a string using the specified encoding
// 以指定的编码方式写入一个字符串
socket.write("some data", "UTF-16");
```

写入操作是异步的，直到调用write方法返回过后一段时间它才发生。

#### 关闭处理器【Close Handler】

若您想要在Socket关闭时收到通知，可（在Socket上）设置一个[closeHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#closeHandler-io.vertx.core.Handler-)：

```java
socket.closeHandler(v -> {
  System.out.println("The socket has been closed");
});
```

#### 处理异常

当Socket发生异常时，您可以设置一个[exceptionHandler](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#exceptionHandler-io.vertx.core.Handler-)来接收任何异常信息。

#### Event Bus写处理器

每个Socket会自动在Event Bus中注册一个处理器，当这个处理器中收到任意Buffer（数据）时，它将（数据）写入到自身。

这使您可以将数据写入到处于完全不同Verticle实例的Socket中，甚至通过将Buffer发送到不同Vert.x实例中处理器绑定的地址上。

处理器地址由[writeHandlerID](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#writeHandlerID--)给出。

#### 本地和远程地址

一个[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)的本地地址可通过[localAddress](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#localAddress--)获取。

一个[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)的远程地址（即连接的另一端的地址）可通过[remoteAddress](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#remoteAddress--)获取。

#### 从类路径中发送文件或资源

文件和类路径资源可以直接使用[sendFile](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#sendFile-java.lang.String-)写入Socket，这是发送文件很有效的方式，若操作系统支持它可以被操作系统内核直接处理。

请阅读[从类路径提供文件](http://vertx.io/docs/vertx-core/java/#classpath)章节了解类路径的限制或禁用它。

#### Streaming Socket

[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)的实例也是[ReadStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/ReadStream.html)和[WriteStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/WriteStream.html)实例，因此它们可以用于将数据抽取到其他读取和写入流中。

有关更多信息，请参阅[流和泵](http://vertx.io/docs/vertx-core/java/#streams)的章节。

#### 升级连接到SSL/TLS

一个非SSL/TLS连接可以通过[upgradeToSsl](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html#upgradeToSsl-io.vertx.core.Handler-)方法升级到SSL/TLS连接。

必须为服务器或客户端配置SSL/TLS才能正常工作，有关详情，请参阅[SSL/TLS](http://vertx.io/docs/vertx-core/java/#ssl)章节。

#### 关闭TCP服务器

调用[close](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServer.html#close--)方法关闭服务器，关闭服务器将关闭所有打开的连接并释放所有服务器资源。

关闭实际上是异步的，直到方法调用返回过后一段时间才会实际关闭，若您想在实际关闭完成时收到通知，那么您可以传递一个处理器。

当关闭完全完成后，这个处理器将被调用：

```java
server.close(res -> {
  if (res.succeeded()) {
    System.out.println("Server is now closed");
  } else {
    System.out.println("close failed");
  }
});
```

#### Verticle中自动清理

若您在Verticle内创建了TCP服务器和客户端，这些服务器和客户端将会在Verticle撤销时自动关闭。

#### 扩展 - 共享TCP服务器

任何TCP服务器中的处理器总是在相同的Event Loop线程上执行。

这意味着如果您在多核的服务器上运行，并且只部署了一个实例，那么您的服务器上最多只能使用一个核。

为了利用更多的服务器核，您将需要部署更多的服务器实例。

您可以在代码中以编程方式实例化更多（Server的）实例：

```java
for (int i = 0; i < 10; i++) {
  NetServer server = vertx.createNetServer();
  server.connectHandler(socket -> {
    socket.handler(buffer -> {
      // Just echo back the data
	  // 仅仅回传数据
      socket.write(buffer);
    });
  });
  server.listen(1234, "localhost");
}
```

或者，如果您使用的是Verticle，您可以通过在命令行上使用`-instances`选项来简单部署更多的服务器实例：

```
vertx run com.mycompany.MyVerticle -instances 10
```

或者使用编程方式部署您的Verticle时：

```java
DeploymentOptions options = new DeploymentOptions().setInstances(10);
vertx.deployVerticle("com.mycompany.MyVerticle", options);
```

一旦你这样做，你将发现echo服务器在功能上与之前相同，但是服务器上的所有核都可以被利用，并且可以处理更多的工作。

在这一点上，您可能会问自己：如何让多台服务器在同一主机和端口上侦听？当你尝试部署一个以上的实例时，您是否会遇到端口冲突？

*Vert.x在这里有一点魔法。*

当您在与现有服务器相同的主机和端口上部署另一个服务器实例时，实际上它并不会尝试创建在同一主机/端口上侦听的新服务器实例。

相反，它内部仅仅维护一个服务器实例，并且当传入连接到达时，它以循环方式将其分发给任何连接处理器。

因此，Vert.x TCP服务器可以扩展可用核，而每个实例保持单线程。

#### 创建TCP客户端

使用所有默认选项创建TCP客户端的最简单方法如下：

```java
NetClient client = vertx.createNetClient();
```

#### 配置一个TCP客户端

如果您不想使用默认值，则可以在创建实例时传入[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)给客户端：

```java
NetClientOptions options = new NetClientOptions().setConnectTimeout(10000);
NetClient client = vertx.createNetClient(options);
```

#### 创建连接

您可以使用[connect](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClient.html#connect-int-java.lang.String-io.vertx.core.Handler-)创建到服务器的连接，请指定服务器的端口和主机，以及在连接成功时使用包含[NetSocket](http://vertx.io/docs/apidocs/io/vertx/core/net/NetSocket.html)的结果调用的处理器，若连接失败，则会发生故障。

```java
NetClientOptions options = new NetClientOptions().setConnectTimeout(10000);
NetClient client = vertx.createNetClient(options);
client.connect(4321, "localhost", res -> {
  if (res.succeeded()) {
    System.out.println("Connected!");
    NetSocket socket = res.result();
  } else {
    System.out.println("Failed to connect: " + res.cause().getMessage());
  }
});
```

#### 配置重连

可以将客户端配置为在无法连接的情况下自动重试连接到服务器，这是使用[setReconnectInterval](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#setReconnectInterval-long-)和[setReconnectAttempts](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#setReconnectAttempts-int-)配置的。

*注意：目前如果连接失败，Vert.x将不尝试重新连接，重新连接尝试和间隔（参数）仅适用于创建初始连接。*

```java
NetClientOptions options = new NetClientOptions().
  setReconnectAttempts(10).
  setReconnectInterval(500);

NetClient client = vertx.createNetClient(options);
```

默认情况下，多个连接尝试被禁用了。

#### 记录网络活动

为了调试，网络活动可以被记录：

```java
NetServerOptions options = new NetServerOptions().setLogActivity(true);

NetServer server = vertx.createNetServer(options);
```

对于客户端：

```java
NetClientOptions options = new NetClientOptions().setLogActivity(true);

NetClient client = vertx.createNetClient(options);
```

Netty使用`DEBUG`级别和`io.netty.handler.logging.LoggingHandler`类记录网络活动，使用网络活动记录时，需要注意以下几点：

* 记录不是由Vert.x的日志器【logging】执行而是由Netty执行
* 这个功能不能用于生产环境

您应该阅读[Netty日志器](http://vertx.io/docs/vertx-core/java/#netty-logging)章节

#### 配置服务器和客户端使用SSL/TLS

TCP客户端和服务器通过配置而使用传输层安全性【[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)】——早期版本的TLS被称为SSL。

无论是否使用SSL/TLS，服务器和客户端的API都是相同的，并且可以传入配置用于创建服务器或客户端的[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)或[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)实例。

##### 在服务器上启用SSL/TLS

SSL/TLS使用[ssl](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html#setSsl-boolean-)来启用。

默认是禁用的。

##### 指定服务器的密钥/证书

SSL/TLS服务器通常向客户端提供证书，以便验证服务器的身份。

可以通过以下几种方式为服务器配置证书/密钥：

第一种方法是指定包含证书和私钥的Java密钥库位置。

可以使用JDK附带的[keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/solaris/keytool.html)实用程序来管理Java密钥受信存储。

还应提供密钥存储的密码：

```java
NetServerOptions options = new NetServerOptions().setSsl(true).setKeyStoreOptions(
  new JksOptions().
    setPath("/path/to/your/server-keystore.jks").
    setPassword("password-of-your-keystore")
);
NetServer server = vertx.createNetServer(options);
```

或者，您可以自己读取密钥库到一个Buffer，并将它直接提供：

```java
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-keystore.jks");
JksOptions jksOptions = new JksOptions().
  setValue(myKeyStoreAsABuffer).
  setPassword("password-of-your-keystore");
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(jksOptions);
NetServer server = vertx.createNetServer(options);
```

PKCS＃12格式的密钥/证书（[http://en.wikipedia.org/wiki/PKCS_12](http://en.wikipedia.org/wiki/PKCS_12)），通常与`.pfx`或`.p12`扩展名也可以与JKS密钥存储相似的方式加载：

```java
NetServerOptions options = new NetServerOptions().setSsl(true).setPfxKeyCertOptions(
  new PfxOptions().
    setPath("/path/to/your/server-keystore.pfx").
    setPassword("password-of-your-keystore")
);
NetServer server = vertx.createNetServer(options);
```

它也支持Buffer的配置：

```java
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-keystore.pfx");
PfxOptions pfxOptions = new PfxOptions().
  setValue(myKeyStoreAsABuffer).
  setPassword("password-of-your-keystore");
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setPfxKeyCertOptions(pfxOptions);
NetServer server = vertx.createNetServer(options);
```

另外一种单独提供服务器私钥和证书的方法是使用`.pem`文件。

```java
NetServerOptions options = new NetServerOptions().setSsl(true).setPemKeyCertOptions(
  new PemKeyCertOptions().
    setKeyPath("/path/to/your/server-key.pem").
    setCertPath("/path/to/your/server-cert.pem")
);
NetServer server = vertx.createNetServer(options);
```

它也支持Buffer的配置：

```java
Buffer myKeyAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-key.pem");
Buffer myCertAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-cert.pem");
PemKeyCertOptions pemOptions = new PemKeyCertOptions().
  setKeyValue(myKeyAsABuffer).
  setCertValue(myCertAsABuffer);
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setPemKeyCertOptions(pemOptions);
NetServer server = vertx.createNetServer(options);
```

请记住pem的配置和私钥是不加密的。

##### 指定服务器信任

SSL/TLS服务器可以使用证书颁发机构来验证客户端的身份。

证书颁发机构可通过多种方式为服务器配置：

可使用JDK随附的[keytool](http://docs.oracle.com/javase/6/docs/technotes/tools/solaris/keytool.html)实用程序来管理Java受信存储。

还应提供受信存储的密码：

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setTrustStoreOptions(
    new JksOptions().
      setPath("/path/to/your/truststore.jks").
      setPassword("password-of-your-truststore")
  );
NetServer server = vertx.createNetServer(options);
```

或者您可以自己读取受信存储到Buffer，并将它直接提供：

```java
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.jks");
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setTrustStoreOptions(
    new JksOptions().
      setValue(myTrustStoreAsABuffer).
      setPassword("password-of-your-truststore")
  );
NetServer server = vertx.createNetServer(options);
```

PKCS＃12格式的密钥/证书（[http://en.wikipedia.org/wiki/PKCS_12](http://en.wikipedia.org/wiki/PKCS_12)），通常与`.pfx`或`.p12`扩展名也可以与JKS受信存储相似的方式加载：

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setPfxTrustOptions(
    new PfxOptions().
      setPath("/path/to/your/truststore.pfx").
      setPassword("password-of-your-truststore")
  );
NetServer server = vertx.createNetServer(options);
```

它也支持Buffer的配置：

```java
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.pfx");
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setPfxTrustOptions(
    new PfxOptions().
      setValue(myTrustStoreAsABuffer).
      setPassword("password-of-your-truststore")
  );
NetServer server = vertx.createNetServer(options);
```

另一种提供服务器证书颁发机构的方法是使用一个列表.pem文件。

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setPemTrustOptions(
    new PemTrustOptions().
      addCertPath("/path/to/your/server-ca.pem")
  );
NetServer server = vertx.createNetServer(options);
```

它也支持Buffer的配置：

```java
Buffer myCaAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/server-ca.pfx");
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setClientAuth(ClientAuth.REQUIRED).
  setPemTrustOptions(
    new PemTrustOptions().
      addCertValue(myCaAsABuffer)
  );
NetServer server = vertx.createNetServer(options);
```

##### 客户端启用SSL/TLS

网络客户端也可以轻松地配置为SSL，使用SSL时，它们和标准套接字的使用具有完全相同的API。

若要启用NetClient上的SSL，可调用函数`setSSL(true)`。

##### 受信客户端配置

若客户端使[trustAll](http://vertx.io/docs/apidocs/io/vertx/core/net/ClientOptionsBase.html#setTrustAll-boolean-)设置为true，则客户端将信任所有服务端证书。连接仍然会被加密，但这种模式很容易受到“中间人”的攻击。即你无法确定你正连接到谁，请谨慎使用。默认值为false。

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setTrustAll(true);
NetClient client = vertx.createNetClient(options);
```

若客户端没设置[trustAll](http://vertx.io/docs/apidocs/io/vertx/core/net/ClientOptionsBase.html#setTrustAll-boolean-)，则必须配置客户端受信存储，并且受信客户端应该包含服务器的证书。

默认情况下，客户端禁用主机验证。要启用主机验证，请将算法设置为在客户端上使用（目前仅支持HTTPS和LDAPS）：

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setHostnameVerificationAlgorithm("HTTPS");
NetClient client = vertx.createNetClient(options);
```

和服务器配置相同，也可通过以下几种方式配置受信客户端：

第一种方法是指定包含证书颁发机构的Java受信库的位置。

它只是一个标准的Java密钥存储，与服务器端的密钥存储相同。通过在[jsk options](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html)上使用[path](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html#setPath-java.lang.String-)设置客户端受信存储位置。如果服务器在连接期间提供不在客户端受信存储中的证书，则尝试连接将不会成功。

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setTrustStoreOptions(
    new JksOptions().
      setPath("/path/to/your/truststore.jks").
      setPassword("password-of-your-truststore")
  );
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.jks");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setTrustStoreOptions(
    new JksOptions().
      setValue(myTrustStoreAsABuffer).
      setPassword("password-of-your-truststore")
  );
NetClient client = vertx.createNetClient(options);
```

通常使用`.pfx`或`.p12`扩展名的PKCS＃12格式（[http://en.wikipedia.org/wiki/PKCS_12](http://en.wikipedia.org/wiki/PKCS_12)）的证书颁发机构也可以与JKS受信存储相似的方式加载：

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPfxTrustOptions(
    new PfxOptions().
      setPath("/path/to/your/truststore.pfx").
      setPassword("password-of-your-truststore")
  );
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/truststore.pfx");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPfxTrustOptions(
    new PfxOptions().
      setValue(myTrustStoreAsABuffer).
      setPassword("password-of-your-truststore")
  );
NetClient client = vertx.createNetClient(options);
```

另一种提供服务器证书颁发机构的方法是使用一个列表.pem文件。

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPemTrustOptions(
    new PemTrustOptions().
      addCertPath("/path/to/your/ca-cert.pem")
  );
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myTrustStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/ca-cert.pem");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPemTrustOptions(
    new PemTrustOptions().
      addCertValue(myTrustStoreAsABuffer)
  );
NetClient client = vertx.createNetClient(options);
```

##### 指定客户端的密钥/证书

如果服务器需要客户端认证，那么当连接时，客户端必须向服务器显示自己的证书。 可通过以下几种方式配置客户端：

第一种方法是指定包含密钥和证书的Java密钥库的位置，它只是一个常规的Java密钥存储。 使用[jks options](http://vertx.io/docs/apidocs/io/vertx/core/net/JksOptions.html)上的功能路径设置客户端密钥库位置。

```java
NetClientOptions options = new NetClientOptions().setSsl(true).setKeyStoreOptions(
  new JksOptions().
    setPath("/path/to/your/client-keystore.jks").
    setPassword("password-of-your-keystore")
);
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-keystore.jks");
JksOptions jksOptions = new JksOptions().
  setValue(myKeyStoreAsABuffer).
  setPassword("password-of-your-keystore");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setKeyStoreOptions(jksOptions);
NetClient client = vertx.createNetClient(options);
```

PKCS＃12格式的密钥/证书（[http://en.wikipedia.org/wiki/PKCS_12](http://en.wikipedia.org/wiki/PKCS_12)），通常与`.pfx`或`.p12`扩展名也可以与JKS密钥存储相似的方式加载：

```java
NetClientOptions options = new NetClientOptions().setSsl(true).setPfxKeyCertOptions(
  new PfxOptions().
    setPath("/path/to/your/client-keystore.pfx").
    setPassword("password-of-your-keystore")
);
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myKeyStoreAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-keystore.pfx");
PfxOptions pfxOptions = new PfxOptions().
  setValue(myKeyStoreAsABuffer).
  setPassword("password-of-your-keystore");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPfxKeyCertOptions(pfxOptions);
NetClient client = vertx.createNetClient(options);
```

另一种单独提供服务器私钥和证书的方法是使用.pem文件。

```java
NetClientOptions options = new NetClientOptions().setSsl(true).setPemKeyCertOptions(
  new PemKeyCertOptions().
    setKeyPath("/path/to/your/client-key.pem").
    setCertPath("/path/to/your/client-cert.pem")
);
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myKeyAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-key.pem");
Buffer myCertAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/client-cert.pem");
PemKeyCertOptions pemOptions = new PemKeyCertOptions().
  setKeyValue(myKeyAsABuffer).
  setCertValue(myCertAsABuffer);
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setPemKeyCertOptions(pemOptions);
NetClient client = vertx.createNetClient(options);
```

请记住pem的配置和私钥是不加密的。

##### 用于测试和开发目的的自签名证书

*警告：不要在生产设置中使用，注意，这里生成的密钥非常不安全。*

通常情况下，无论是单位/集成测试还是运行应用程序的开发版本都需要自签名证书。

[SelfSignedCertificate](http://vertx.io/docs/apidocs/io/vertx/core/net/SelfSignedCertificate.html)可用于提供自签名PEM证书，并可以传递[KeyCertOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/KeyCertOptions.html)和[TrustOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/TrustOptions.html)配置（给它）：

```java
SelfSignedCertificate certificate = SelfSignedCertificate.create();

NetServerOptions serverOptions = new NetServerOptions()
  .setSsl(true)
  .setKeyCertOptions(certificate.keyCertOptions())
  .setTrustOptions(certificate.trustOptions());

NetServer server = vertx.createNetServer(serverOptions)
  .connectHandler(socket -> socket.write("Hello!").end())
  .listen(1234, "localhost");

NetClientOptions clientOptions = new NetClientOptions()
  .setSsl(true)
  .setKeyCertOptions(certificate.keyCertOptions())
  .setTrustOptions(certificate.trustOptions());

NetClient client = vertx.createNetClient(clientOptions);
client.connect(1234, "localhost", ar -> {
  if (ar.succeeded()) {
    ar.result().handler(buffer -> System.out.println(buffer));
  } else {
    System.err.println("Woops: " + ar.cause().getMessage());
  }
});
```

客户端也可配置为信任所有证书：

```java
NetClientOptions clientOptions = new NetClientOptions()
  .setSsl(true)
  .setTrustAll(true);
```

注意：自签名证书也适用于其他TCP协议，如HTTPS：

```java
SelfSignedCertificate certificate = SelfSignedCertificate.create();

vertx.createHttpServer(new HttpServerOptions()
  .setSsl(true)
  .setKeyCertOptions(certificate.keyCertOptions())
  .setTrustOptions(certificate.trustOptions()))
  .requestHandler(req -> req.response().end("Hello!"))
  .listen(8080);
```

##### 待撤销证书颁发机构

可以将信任配置为对不应再受信任的待撤销证书使用证书吊销列表（CRL），[crlPath](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html#addCrlPath-java.lang.String-)配置CRL列表以使用：

```java
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setTrustStoreOptions(trustOptions).
  addCrlPath("/path/to/your/crl.pem");
NetClient client = vertx.createNetClient(options);
```

它也支持Buffer的配置：

```java
Buffer myCrlAsABuffer = vertx.fileSystem().readFileBlocking("/path/to/your/crl.pem");
NetClientOptions options = new NetClientOptions().
  setSsl(true).
  setTrustStoreOptions(trustOptions).
  addCrlValue(myCrlAsABuffer);
NetClient client = vertx.createNetClient(options);
```

##### 配置密码套件【Cipher Suite】

默认情况下，TLS配置将使用运行Vert.x的JVM密码套件，该密码套件可以配置一套启用的密码：

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(keyStoreOptions).
  addEnabledCipherSuite("ECDHE-RSA-AES128-GCM-SHA256").
  addEnabledCipherSuite("ECDHE-ECDSA-AES128-GCM-SHA256").
  addEnabledCipherSuite("ECDHE-RSA-AES256-GCM-SHA384").
  addEnabledCipherSuite("CDHE-ECDSA-AES256-GCM-SHA384");
NetServer server = vertx.createNetServer(options);
```

密码套件可在[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)或[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)配置项中指定。

##### 配置TLS协议版本

默认情况下，TLS配置将使用以下协议版本：SSLv2Hello、TLSv1、TLSv1.1和TLSv1.2。 协议版本可以通过显式添加启用协议进行配置：

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(keyStoreOptions).
  addEnabledSecureTransportProtocol("TLSv1.1").
  addEnabledSecureTransportProtocol("TLSv1.2");
NetServer server = vertx.createNetServer(options);
```

协议版本可在[NetServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetServerOptions.html)或[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)配置项中指定。

##### SSL引擎

引擎实现可以配置为使用OpenSSL而不是JDK实现（来支持SSL）。 OpenSSL提供比JDK引擎更好的性能和CPU使用率、以及JDK版本独立性。

引擎选项的使用是：

* [getSslEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/TCPSSLOptions.html#getSslEngineOptions--)选项设置时
* 否则[JdkSSLEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/JdkSSLEngineOptions.html)

```java
NetServerOptions options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(keyStoreOptions);

// Use JDK SSL engine explicitly
// 显式使用JDK SSL引擎
options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(keyStoreOptions).
  setJdkSslEngineOptions(new JdkSSLEngineOptions());

// Use OpenSSL engine
// 使用OpenSSL引擎
options = new NetServerOptions().
  setSsl(true).
  setKeyStoreOptions(keyStoreOptions).
  setOpenSslEngineOptions(new OpenSSLEngineOptions());
```

##### 应用层协议协商【ALPN】

ALPN【Application-Layer Protocol Negotiation】是应用层协议协商的TLS扩展，它被HTTP/2使用：在TLS握手期时，客户端给出其接受的应用协议列表，并且服务器使用（自身）支持的协议响应。

标准的Java 8不支持ALPN，所以ALPN应该通过其他方式启用：

* OpenSSL支持
* Jetty-ALPN支持

引擎选项可使用:

* [getSslEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/TCPSSLOptions.html#getSslEngineOptions--)选项设置时
* JDK中ALPN可用时使用[JdkSSLEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/JdkSSLEngineOptions.html)
* OpenSSL中ALPN可用时使用[OpenSSLEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/OpenSSLEngineOptions.html)
* 否则失败

**OpenSSL ALPN支持**

OpenSSL提供了原生【Native】的ALPN支持。

OpenSSL需要配置[setOpenSslEngineOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/TCPSSLOptions.html#setOpenSslEngineOptions-io.vertx.core.net.OpenSSLEngineOptions-)并在类路径上使用[netty-tcnative](http://netty.io/wiki/forked-tomcat-native.html)的jar库。依赖于tcnative的实现它需要OpenSSL安装在您的操作系统中。

**Jetty-ALPN支持**

Jetty-ALPN是一个小型的jar，它覆盖了几种Java 8发行版用以支持ALPN。

JVM必须将`alpn-boot-${version}.jar`放在它的bootclasspath中启动：

```
-Xbootclasspath/p:/path/to/alpn-boot${version}.jar
```

其中${version}取决于JVM的版本，如*OpenJDK 1.8.0u74*中的*8.1.7.v20160121*，这个完整列表可以在[Jetty-ALPN](http://www.eclipse.org/jetty/documentation/current/alpn-chapter.html)页面上找到。

主要缺点就是版本取决于JVM。

为了解决这个问题，可以使用[Jetty ALPN agent](https://github.com/jetty-project/jetty-alpn-agent)。agent是一个JVM代理，它会为运行它的JVM选择正确的ALPN版本：

```
-javaagent:/path/to/alpn/agent
```

#### 客户端连接使用代理

[NetClient](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClient.html)支持HTTP/1.x *CONNECT*、*SOCKS4a*或*SOCKS5*代理。

代理可以在[NetClientOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/NetClientOptions.html)内设置[ProxyOptions](http://vertx.io/docs/apidocs/io/vertx/core/net/ProxyOptions.html)来配置代理类型、主机名、端口、可选的用户名和密码。

以下是一个例子：

```java
NetClientOptions options = new NetClientOptions()
  .setProxyOptions(new ProxyOptions().setType(ProxyType.SOCKS5)
    .setHost("localhost").setPort(1080)
    .setUsername("username").setPassword("secret"));
NetClient client = vertx.createNetClient(options);
```

DNS解析会一直在代理服务器上执行，为了实现SOCKS4客户端的功能，需要先在本地解析DNS地址。

### 编写HTTP服务器和客户端

Vert.x允许您轻松编写非阻塞HTTP客户端和服务器。

Vert.x支持HTTP/1.0、HTTP/1.1和HTTP/2协议。

用于HTTP的基本API对HTTP/1.x和HTTP/2是相同的，特定的API功能也可用于处理HTTP/2协议。

#### 创建一个HTTP服务器

使用所有默认选项创建HTTP服务器的最简单方法如下：

```java
HttpServer server = vertx.createHttpServer();
```

#### 配置一个HTTP服务器

若您不想用默认值，可以在创建服务器时传递一个[HttpServerOptions](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerOptions.html)实例给它：

```java
HttpServerOptions options = new HttpServerOptions().setMaxWebsocketFrameSize(1000000);

HttpServer server = vertx.createHttpServer(options);
```

#### 配置一个HTTP/2的服务器

Vert.x支持TLS `h2`和TCP `h2c`之上的HTTP/2协议。

* `h2`用于通过应用层协议协商（ALPN）协商的TLS时识别HTTP/2协议
* `h2c`在TCP上以明文形式使用时识别HTTP/2协议，这样的连接是使用HTTP/1.1升级请求或直接建立的

要处理h2请求，TLS必须调用[setUseAlpn](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerOptions.html#setUseAlpn-boolean-)启用：

```java
HttpServerOptions options = new HttpServerOptions()
    .setUseAlpn(true)
    .setSsl(true)
    .setKeyStoreOptions(new JksOptions().setPath("/path/to/my/keystore"));

HttpServer server = vertx.createHttpServer(options);
```

ALPN是一个TLS的扩展，它在客户端和服务器开始交换数据之前协商协议。

不支持ALPN的客户端仍然可以执行经典的SSL握手。

通常情况，ALPN会对`h2`协议达成一致，尽管服务器或客户端决定了仍然使用HTTP/1.1协议。

要处理`h2c`请求，TLS必须被禁用，服务器将升级到HTTP/2以满足任何希望升级到HTTP/2的HTTP/1.1请求。它还将接受以`PRI*HTTP/2.0\r\nSM\r\n`开始的`h2c`直接连接。

*警告：大多数浏览器不支持h2c，所以在服务网站时，您应该使用h2而不是h2c。*

当服务器接受HTTP/2连接时，它会向客户端发送其初始设置。定义客户端如何使用连接，服务器的默认初始设置为：

* [getMaxConcurrentStreams](http://vertx.io/docs/apidocs/io/vertx/core/http/Http2Settings.html#getMaxConcurrentStreams--)：按照HTTP/2 RFC建议推荐值为100
* 其他默认的HTTP/2的设置

*注意：Worker Verticles和HTTP/2不兼容*

#### 记录网络服务器活动

为了进行调试，可记录网络活动。

```java
HttpServerOptions options = new HttpServerOptions().setLogActivity(true);

HttpServer server = vertx.createHttpServer(options);
```

有关详细说明，请参阅有[记录网络活动](http://vertx.io/docs/vertx-core/java/#logging_network_activity)章节。

#### 开始服务器监听

要告诉服务器监听传入的请求，您可以使用其中一个[listen](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServer.html#listen--)方式。

在配置项中告诉服务器监听指定的主机和端口：

```java
HttpServer server = vertx.createHttpServer();
server.listen();
```

或在调用`listen`时指定主机和端口号，这样就忽略了配置项（中的主机和端口）：

```java
HttpServer server = vertx.createHttpServer();
server.listen(8080, "myhost.com");
```

默认主机名是`0.0.0.0`，它表示：监听所有可用地址；默认端口号是`80`。

实际的绑定也是异步的，因此服务器也许并没有在调用listen返回时监听，而是在一段时间过后（才监听）。

若您希望在服务器实际监听时收到通知，您可以向listen提供一个处理器。例如：

```java
HttpServer server = vertx.createHttpServer();
server.listen(8080, "myhost.com", res -> {
  if (res.succeeded()) {
    System.out.println("Server is now listening!");
  } else {
    System.out.println("Failed to bind!");
  }
});
```

#### 收到传入请求【Incoming requests】的通知

若您需要在收到请求时收到通知，则需要设置一个[requestHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServer.html#requestHandler-io.vertx.core.Handler-)

```java
HttpServer server = vertx.createHttpServer();
server.requestHandler(request -> {
  // Handle the request in here
  // 在这里处理请求
});
```

#### 处理请求

当请求到达时，它传入一个称为[HttpServerRequest](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html)的实例并调用请求处理器，此对象表示服务端HTTP请求。

当请求的头信息被完全读取时调用该处理器。

如果请求包含请求体，那么该请求体将在请求处理器被调用后的某个时间到达服务器。

服务请求对象允许您检索[uri](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#uri--)，[path](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#path--)，[params](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#params--)和[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)等其他事。

每一个服务请求对象和一个服务响应对象绑定，您可以用[response](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#response--)获取一个[HttpServerResponse](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerResponse.html)对象的引用。

这是服务器处理请求并回复“hello world”的简单示例。

```java
vertx.createHttpServer().requestHandler(request -> {
  request.response().end("Hello world");
}).listen(8080);
```

**请求版本【version】**

在请求中指定的HTTP版本可通过[version](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#version--)获取。

**请求方法【method】**

使用[method](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#method--)读取请求中的HTTP方法（即GET、POST、PUT、DELETE、HEAD、OPTIONS等）。

**请求URI【uri】**

使用[uri](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#uri--)读取请求中的URI路径。

请注意，这是在HTTP请求中传递的实际URI，它总是一个相对的URI。

这个URI是在[Section 5.1.2 of the HTTP specification - Request-URI](http://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html)中定义的。

**请求路径【path】**

使用[path](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#path--)读取URI中的路径部分。

例如，请求的URI为：

```
a/b/c/page.html?param1=abc&param2=xyz
```

路径部分应该是：

```
/a/b/c/page.html
```

**请求查询【query】**

使用[query](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#query--)读取URI中的查询部分。

例如，请求的URI为：

```
a/b/c/page.html?param1=abc&param2=xyz
```

查询部分应该是：

```
param1=abc&param2=xyz
```

**请求头【headers】**

使用[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)获取HTTP请求中的请求头信息。

这个方法返回一个[MultiMap](http://vertx.io/docs/apidocs/io/vertx/core/MultiMap.html)的实例——它像一个普通的Map或Hash、单它还允许同一个键支持多个值——因为HTTP允许同一个键支持多个请求头的值。

它还具有不区分大小写的键，这意味着您可以执行以下操作：

```java
MultiMap headers = request.headers();

// Get the User-Agent:
// 读取User-Agent
System.out.println("User agent is " + headers.get("user-agent"));

// You can also do this and get the same result:
// 这样做可以得到和上边相同的结果
System.out.println("User agent is " + headers.get("User-Agent"));
```

**请求主机【host】**

使用[host](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#host--)返回HTTP请求中的主机名。

对于HTTP/1.x请求返回请求头中的`host`值，对于HTTP/1请求则返回伪头中的`:authority`的值。

**请求参数【Parameters】**

使用[params](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#params--)返回HTTP请求中的参数信息。

像[headers](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#headers--)一样它也会返回一个[MultiMap](http://vertx.io/docs/apidocs/io/vertx/core/MultiMap.html)，因为可以有多个具有相同名称的参数。

请求参数在请求URI的path部分之后，例如URI是：

```
/page.html?param1=abc&param2=xyz
```

那么参数将包含以下内容：

```
param1: 'abc'
param2: 'xyz'
```

请注意，这些请求参数是从请求的URI中解析读取的，若您已经将表单属性作为在`multi-part/form-data`请求正文中提交的HTML表单的一部分发送，那么它们将不会显示在此处的参数中。

**远程地址**

可以使用[remoteAddress](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#remoteAddress--)读取请求发送者的地址。

**绝对URI**

HTTP请求中传递的URI通常是相对的，若您想要读取请求中和相对URI对应的绝对URI，可调用[absoluteURI](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#absoluteURI--)

**结束处理器【End Handler】**

当整个请求（包括任何正文）已经被完全读取时，请求中的[endHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#endHandler-io.vertx.core.Handler-)会被调用。

**请求体中读取数据**

HTTP请求通常包含我们需要读取的主体。如前所述，当请求头部达到时，请求处理器会被调用，因此请求对象在此时没有请求体。

这是因为请求体可能非常大（如文件上传），并且我们不会在内容发送给您之前将其全部缓冲存储在内存中，这可能会导致服务器耗尽可用内存。

要接收请求体，您可在请求中设置一个处理器[handler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#handler-io.vertx.core.Handler-)，每次请求体的一小块达到时，该处理器都会被调用。以下是一个例子：

```java
request.handler(buffer -> {
  System.out.println("I have received a chunk of the body of length " + buffer.length());
});
```

传递给处理器的对象是一个[Buffer](http://vertx.io/docs/apidocs/io/vertx/core/buffer/Buffer.html)，当数据从网络到达时，处理器可以多次被调用，这取决于请求体的大小。

在某些情况下（例：若请求体很小），您将需要将这个请求体聚合到内存中，以便您可以按照下边的方式进行聚合：


```java
Buffer totalBuffer = Buffer.buffer();

request.handler(buffer -> {
  System.out.println("I have received a chunk of the body of length " + buffer.length());
  totalBuffer.appendBuffer(buffer);
});

request.endHandler(v -> {
  System.out.println("Full body received, length = " + totalBuffer.length());
});
```

这是一个常见的情况，Vert.x为您提供了一个[bodyHandler](http://vertx.io/docs/apidocs/io/vertx/core/http/HttpServerRequest.html#bodyHandler-io.vertx.core.Handler-)来执行此操作，当所有请求体被收到时，bodyHandler处理器程序会被调用一次：

```java
request.bodyHandler(totalBuffer -> {
  System.out.println("Full body received, length = " + totalBuffer.length());
});
```

**Pumping请求**

请求对象是一个[ReadStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/ReadStream.html)，因此您可以将请求体引导到任何[WriteStream](http://vertx.io/docs/apidocs/io/vertx/core/streams/WriteStream.html)实例中。

有关详细的说明，请参阅[流和泵](http://vertx.io/docs/vertx-core/java/#streams)的章节。

**处理HTML表单**

您可使用内容类型为`application/x-www-form-urlencoded`或`multipart/form-data`提交HTML表单。



## 引用

1. Vert.x的扩展包是Vert.x的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。
2. 两种常用的项目构建工具。
3. Don't call us, we'll call you，Call斟酌了很久是翻译还是不翻译，”调用“在这个标题中听起来过于生硬，为了辅助理解没有翻译。

## 注释

1. 在这个章节中worker pool和blocking code pool应该是等价概念，就是工作线程池，专用于执行阻塞式代码的线程集合。
2. worker executor和executor在这个章节也就是等价概念，创建一个执行器创建额外的工作线程池，执行器用来管理这个额外的线程池。

## 结语
