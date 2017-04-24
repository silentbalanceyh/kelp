## 6. API Usage最佳实践

Tradeshift REST API可以有很多用途，这儿有一些开发过程中的最佳实践。

### 6.1.客户端考虑

* 在所有请求中发送User-Agent请求头，该请求头可用于标识您的客户端、版本，如：BananaCorpClient/3.4；
* 在所有请求中发送Accept请求头如application/json、text/xml来标识客户端偏好；
* 所有的请求都可能因为某些原因而失败，对于应用错误，通常使用相同的错误结构，所以处理错误的方式也统一。

### 6.2.文档同步





