## 6. API Usage最佳实践

Tradeshift REST API可以有很多用途，这儿有一些开发过程中的最佳实践。

### 6.1.客户端考虑

* 在所有请求中发送User-Agent请求头，该请求头可用于标识您的客户端、版本，如：BananaCorpClient/3.4；
* 在所有请求中发送Accept请求头如application/json、text/xml来标识客户端偏好；
* 所有的请求都可能因为某些原因而失败，对于应用错误，通常使用相同的错误结构，所以处理错误的方式也统一。

### 6.2.文档同步

* 常用的API主要用于文档同步，有可能直接同步改变的文档资源部分。这里有两种策略：
  * 从上一次Poll到这一次Poll的改变，这种情况仅仅做一个Poll，保存时间戳，包括下一次Poll中的`since`查询参数。
  * 针对不可知的文档执行Poll，当Poll不可知的文档时，直接执行一次Poll，迭代所有收到的文档，使用地址`external/documents/{documentId}/tags/{tag}`资源来PUT一个自定义的Tag到文档中，如



