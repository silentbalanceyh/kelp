因为头信息可以在请求中发送，并且可以从响应中接收，所以这些信息会有一定的限制，为了不更改Header的信息，所以提供了和它相关的Guard属性。这个属性不是对Web应用开放的，但它限制了是否可更改Header的一些行为。

可用的Guard值包括：

* none：默认值
* request：从一个Request中可读取的Guard信息（[Request.headers](https://developer.mozilla.org/en-US/docs/Web/API/Request/headers)）
* request-no-cors：使用了[Request.mode](https://developer.mozilla.org/en-US/docs/Web/API/Request/mode) = no-cors创建的一个请求，并从该请求中读取Header的Guard信息
* response：直接从响应中读取的Header对应Guard信息（[Response.headers](https://developer.mozilla.org/en-US/docs/Web/API/Response/headers)）
* immutable：大部分时间用于ServiceWorkers，将Header处理成只读。

_**NOTES**_：对于Header值，您不可追加或者设置`Content-Length`，类似的，在Response头信息中添加`Set-Cookie`也是不允许的，ServiceWorkers同样也不允许在响应中同步设置`Set-Cookie`。



