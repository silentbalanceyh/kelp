根据您所看到的上述内容，当fetch\(\)方法的Promise被处理（Resolve）时，它将会返回一个[Response](https://developer.mozilla.org/en-US/docs/Web/API/Response)实例。这个对象同样可通过JavaScript编程的方式创建，但是当您通过[respondWith\(\)](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith)方法接收了自定义的请求时，这种方式在[ServiceWorkers](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API)中不生效。

```javascript
var myBody = new Blob();

addEventListener('fetch', function(event) {
  event.respondWith(
    new Response(myBody, {
      headers: { "Content-Type" : "text/plain" }
    })
  );
});
```

构造函数Response\(\)可传入两个可选参数——一个Response的Body（响应正文），一个初始化专用Options对象（和Request、fetch第二参数一样）。下边这些response的属性是您通常会使用到的：



