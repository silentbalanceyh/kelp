# Vert.x Core Manual

## 中英对照表

* Client：客户端
* Server：服务端
* Writing：编写（有些地方译为开发）
* Reactor：反应堆
* Options：配置项
* Context：上下文环境
* Undeploy：撤销（部署）
* Destroyed：销毁
* Handler/Handle：处理器/处理
* Block：阻塞
* Out of Box：标准环境（开箱即用）

_注意：Vert.x和Vertx的区别：文中所有Vert.x概念使用标准单词Vert.x，而Vertx通常表示Java中的类：_`io.vertx.core.Vertx`_。_

## 正文

Vert.x内部的Java API集合我们称为**Vert.x Core**，[文档地址](https://github.com/eclipse/vert.x)。

Vert.x Core提供了下列功能

* 编写TCP客户端和服务端
* 编写HTTP客户端和支持WebSocket的服务端
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

如果你喜欢可以直接实现该接口，但是通常直接从抽象类[AbstractVerticle](http://vertx.io/docs/apidocs/io/vertx/core/AbstractVerticle.html)继承更简单。

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




## 引用

1. Vert.x的扩展包是Vert.x的子项目集合，类似[Web](http://vertx.io/docs/#web)、[Web Client](http://vertx.io/docs/#web-client)、[Data Access](http://vertx.io/docs/#data_access)等。
2. 两种常用的项目构建工具。
3. Don't call us, we'll call you，Call斟酌了很久是翻译还是不翻译，”调用“在这个标题中听起来过于生硬，为了辅助理解没有翻译。

## 注释

1. 在这个章节中worker pool和blocking code pool应该是等价概念，就是工作线程池，专用于执行阻塞式代码的线程集合。
2. worker executor和executor在这个章节也就是等价概念，创建一个执行器创建额外的工作线程池，执行器用来管理这个额外的线程池。

## 结语



