Headers接口允许您通过Headers\(\)方法创建自定义的HTTP头信息，一个Header对象就是简单的键值对：

```javascript
var content = "Hello World";
var myHeaders = new Headers();
myHeaders.append("Content-Type", "text/plain");
myHeaders.append("Content-Length", content.length.toString());
myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
```

同样的创建方法，您可以通过一个JavaScript对象来构造这样的Header：

```javascript
myHeaders = new Headers({
  "Content-Type": "text/plain",
  "Content-Length": content.length.toString(),
  "X-Custom-Header": "ProcessThisImmediately",
});
```

这些设置的内容可以通过查询和获取拿到：

```javascript
console.log(myHeaders.has("Content-Type")); // true
console.log(myHeaders.has("Set-Cookie")); // false
myHeaders.set("Content-Type", "text/html");
myHeaders.append("X-Custom-Header", "AnotherValue");

console.log(myHeaders.get("Content-Length")); // 11
console.log(myHeaders.get("X-Custom-Header")); // ["ProcessThisImmediately", "AnotherValue"]

myHeaders.delete("X-Custom-Header");
console.log(myHeaders.get("X-Custom-Header")); // [ ]
```

上边有些操作仅仅对[ServiceWorkers](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API)生效，并且提供了更友好的API来操作Header。

如果提供的HTTP头信息并不是一个合法的HTTP头键，则这个头方法会抛出一个TypeError错误，若这里存在一个不可变的Guard，改变它的操作也会引起TypeError：

```javascript
var myResponse = Response.error();
try {
  myResponse.headers.set("Origin", "http://mybank.com");
} catch(e) {
  console.log("Cannot pretend to be a bank!");
}
```

更好的使用头的场景是检查头内容是否合法：

```javascript
fetch(myRequest).then(function(response) {
  var contentType = response.headers.get("content-type");
  if(contentType && contentType.indexOf("application/json") !== -1) {
    return response.json().then(function(json) {
      // process your JSON further
    });
  } else {
    console.log("Oops, we haven't got JSON!");
  }
});
```



