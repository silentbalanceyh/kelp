# KM10015 - Fetch API

这是目前一项实验性技术，暂时还没有稳定，可检查[兼容表](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API#Browser_compatibility)来查看对各种浏览器的支持，既然是实验性技术那么很有可能在不久的将来发生改变。Fetch API提供了读取资源的同意接口（包括从网络访问），它将和传统的XMLHttpRequest类似，但是提供的API会更加强大。

## 1.概念和使用

Fetch提供了同样的[Request](https://developer.mozilla.org/en-US/docs/Web/API/Request)和[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)的对象定义（和其他网络请求一样），它将允许根据将来任何需要让您使用它。不论是否Service Worker，Cache API或者其他修改以及读取请求和响应的事，任何用户场景也许都需要您根据您自己的需要通过程序生成相关响应。

它同样提供了相关概念如CORS以及HTTP origin头的语义定义，取代了在其他地方的独立定义。要进行请求和资源获取，请使用[GlobalFetch.fetch](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch)方法，它在多个接口中实现，特别是[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)和[WorkerGlobalScope](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope)，这使得它可以在几乎任何想要获取的资源的上下文环境中使用。

fetch\(\)方法需要一个必须参数——即您需要获取的资源的路径，无论是否成功，它返回一个Promise可用于处理（Resolve）请求对应的响应。您还可以选择传递初始化的options对象作为第二参数，参考[Request](https://developer.mozilla.org/en-US/docs/Web/API/Request)。一旦检索到[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)，则有许多方法可用于定义[Body](https://developer.mozilla.org/en-US/docs/Web/API/Body)内容以及如何处理。您同样可直接使用Request和Response直接创建请求响应对象，但是您不可能直接执行这种操作，相反这些更可能被创建为其他API操作的结果（如来自Service Worker的[FetchEvent.respondWith](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith)。

_**NOTES**_：对于一些更多信息可参考下边的Fetch和Fetch基础概念章节。

### 1.1.Fetch提供的接口

* GlobalFetch：包含了fetch\(\)方法用于读取资源
* Headers：用于描述请求响应头，允许您根据不同结果的不行行为查询它
* Request：用于描述资源请求
* Response：用于描述请求对应的响应

### 1.2.Fetch Mixin

* Body：提供了和Body相关的请求响应处理方法，允许您定义请求本身的类型（Content-Type）或如何处理。

## 2. Fetch基础概念

（未完成）Fetch是一个和XMLHttpRequest等价的现在概念，它提供了和XMLHttpRequest相同的各种功能，但是设计得更具扩展性和高效性，本章节就介绍了一些Fetch过程中的基本概念。

### 2.1.简言

Fetch的核心是HTTP请求、响应、头和正文的抽象接口，以及用于启动异步资源的全局获取方法。由于HTTP的主要组件一般被抽象成JavaScript对象，因此其他API可以很简易使用它的功能。

Service Worker是一个很好的使用Fetch的API的功能，Fetch将这种请求的异步性质进一步提升，API则完全基于Promise。

### 2.2. Guard

Guard是Headers对象的一个特征，它具有不可变、请求、不跨域请求、响应、或无可能的值，具体取决于头的使用位置。当使用Headers\(\)构造函数创建一个新的头对象时，其Guard的值设置为none（默认值），当创建一个请求或响应对象时，它有一个关联的标题对象，其保护设置如下：

| 新对象类型 | 构造函数 | 和Header相关的Guard使用域 |
| :--- | :--- | :--- |
| Request | Request\(\) | request |
|  | Request\(\)和 mode = no-cors | request-no-cors |
| Response | Response\(\) | response |
|  | error\(\) / redirect\(\) | immutable |

一个Header的Guard会影响`set(), delete(), append()`方法，如果您将尝试修改不可变的Header对象，则会抛出TypeError，如果这样做操作会失败。

* guard是一个请求request，它的头名（header name）不可以是[禁止头名称](https://developer.mozilla.org/en-US/docs/Glossary/forbidden_header_name)。
* guard是一个非跨域请求request-no-cors，它的头键值对（name/value）是[简单的头](https://developer.mozilla.org/en-US/docs/Glossary/simple_header)。
* guard是一个响应，它的头名（header name）不可是[禁止响应头名](https://developer.mozilla.org/en-US/docs/Glossary/forbidden_response_header_name)。

## 3. Fetch的使用

Fetch API提供了JavaScript接口用来操作控制HTTP中的管道（如Request何Response），它提供了一个fetch\(\)全局方法可以用简易、更具逻辑的方法从网络上异步读取数据。



