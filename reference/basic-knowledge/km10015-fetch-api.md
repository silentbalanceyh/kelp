# KM10015 - Fetch API

这是目前一项实验性技术，暂时还没有稳定，可检查[兼容表](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API#Browser_compatibility)来查看对各种浏览器的支持，既然是实验性技术那么很有可能在不久的将来发生改变。Fetch API提供了读取资源的同意接口（包括从网络访问），它将和传统的XMLHttpRequest类似，但是提供的API会更加强大。

## 概念和使用

Fetch提供了同样的[Request](https://developer.mozilla.org/en-US/docs/Web/API/Request)和[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)的对象定义（和其他网络请求一样），它将允许根据将来任何需要让您使用它。不论是否Service Worker，Cache API或者其他修改以及读取请求和响应的事，任何用户场景也许都需要您根据您自己的需要通过程序生成相关响应。

它同样提供了相关概念如CORS以及HTTP origin头的语义定义，取代了在其他地方的独立定义。要进行请求和资源获取，请使用[GlobalFetch.fetch](https://developer.mozilla.org/en-US/docs/Web/API/GlobalFetch/fetch)方法，它在多个接口中实现，特别是[Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)和[WorkerGlobalScope](https://developer.mozilla.org/en-US/docs/Web/API/WorkerGlobalScope)，这使得它可以在几乎任何想要获取的资源的上下文环境中使用。

fetch\(\)方法需要一个必须参数——即您需要获取的资源的路径，无论是否成功，它返回一个Promise可用于处理（Resolve）请求对应的响应。您还可以选择传递初始化的options对象作为第二参数，参考[Request](https://developer.mozilla.org/en-US/docs/Web/API/Request)。一旦检索到[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)，则有许多方法可用于定义[Body](https://developer.mozilla.org/en-US/docs/Web/API/Body)内容以及如何处理。您同样可直接使用Request和Response直接创建请求响应对象，但是您不可能直接执行这种操作，相反这些更可能被创建为其他API操作的结果（如来自Service Worker的[FetchEvent.respondWith](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith)。

_**NOTES**_：对于一些更多信息可参考下边的Fetch和Fetch基础概念章节。

## Fetch提供的接口





