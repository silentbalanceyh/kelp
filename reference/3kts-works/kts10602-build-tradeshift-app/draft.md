## 6. API Usage最佳实践

Tradeshift REST API可以有很多用途，这儿有一些开发过程中的最佳实践。

### 6.1.客户端考虑

* 在所有请求中发送User-Agent请求头，该请求头可用于标识您的客户端、版本，如：BananaCorpClient/3.4；
* 在所有请求中发送Accept请求头如application/json、text/xml来标识客户端偏好；
* 所有的请求都可能因为某些原因而失败，对于应用错误，通常使用相同的错误结构，所以处理错误的方式也统一。

### 6.2.文档同步

* 常用的API主要用于文档同步，有可能直接同步改变的文档资源部分。这里有两种策略：
  * 从上一次Poll到这一次Poll的改变，这种情况仅仅做一个Poll，保存时间戳，包括下一次Poll中的`since`查询参数。
  * 针对不可知的文档执行Poll，当Poll不可知的文档时，直接执行一次Poll，迭代所有收到的文档，使用地址`external/documents/{documentId}/tags/{tag}`资源来PUT一个自定义的Tag到文档中，如`POLLED_BY_BANANACORP`，所有针对external/documents的资源应该不包括`tag=POLLED_BY_BANANACORP`查询参数，仅仅接收没有对应tag的文档。
* There的API是没有限制的——也就是说您不需要对所有的请求都过于小心，因为文档Transfer过程在Tradeshift中是异步调用，这儿不需要Poll太多因为每隔5分钟可以同步一次。
* **Sending Documents**：当发送文档时，使用了异步调用方式处理，即使分发请求返回了200 OK代码，或者这个分发【dispatch】之后会失败，如Email边界、接收方的自定义验证规则等。若您想要确认文档被发送，则可访问：`external/documents/{documentId}/dispatches/latest`资源地址直到状态变成`COMPLETED`，若状态变成了`FAILED`，则dispatch过程失败，响应信息中还会包含其他错误信息。如果它是`FAILED_TRANSIENT`，则dispatch失败，所以它可以制动进行。



