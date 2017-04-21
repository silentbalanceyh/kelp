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

Fetch API提供了JavaScript接口用来操作控制HTTP中的管道（如Request何Response），它提供了一个fetch\(\)全局方法可以用简易、更具逻辑的方法从网络上异步读取数据。这类功能之前一般通过[XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)来提供，Fetch则提供了更好的方法可以让您更容易使用这种技术，如[Service Workers](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorker_API)。Fetch同样提供了HTTP相关概念的简单逻辑定义，如CORS和扩展（Extension）。

需要注意的是Fetch规范和jQuery.ajax\(\)中定义的主要有两点不同：

* 即使响应返回了404或500，fetch\(\)中的Promise不会执行HTTP错误状态事件的Reject。相反，它使用通常的方式Resolve（将OK设成false），它仅仅会Reject一些网络错误或者其他防止请求完成的错误。
* 默认情况，fetch不会发送或接收任何服务器相关的Cookie，对于未认证的请求通常这个站点自身需要管理以及维护用户会话状态（发送Cookies，提供credentials头）

### 3.1. 创建fetch请求

一个基本的fetch请求可通过下边代码简单创建：

```javascript
var myImage = document.querySelector('img');

fetch('flowers.jpg')
.then(function(response) {
  return response.blob();
})
.then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```

这里我们从网络上获取图片资源，然后填充到`<img>`元素中，最简单的使用fetch的方式仅需要一个参数——您想获取的资源路径的地址——并且返回一个包含了Response对象的Promise。当然这是一个HTTP的响应对象，并不是实际的图片，若要图片内容则需要从响应中抽取数据。这里使用了blob\(\)方法读取二进制数据（在`Body`中定义，Request和Response都提供了相关实现）。

_**NOTES**_：Body mixin中提供了很多和Body正文相近的处理方法，具体可参考Body部分。

在Blob中读取数据时会包含一个`objectURL`对象，它会插入到`<img/>`标签内。Fetch请求是由Content Security Policy的connect-src指令控制，而不是检索其他资源指令。

#### 3.1.1.提供请求选项options

fetch\(\)方法可接收可选的第二参数，该参数允许您控制一些不同的设置：

```javascript
var myHeaders = new Headers();

var myInit = { method: 'GET',
               headers: myHeaders,
               mode: 'cors',
               cache: 'default' };

fetch('flowers.jpg', myInit)
.then(function(response) {
  return response.blob();
})
.then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```

参考fetch\(\)的API查看详细的选项以及相关描述。

#### 3.1.2.发送带credential的请求

若您想要发送一个带credential的浏览器请求，则可添加`credentials: 'include`在第二参数中，传给fetch\(\)方法：

```javascript
fetch('https://example.com', {
  credentials: 'include'  
})
```

相反若浏览器请求不包含credential信息，则可使用`credentials: 'omit'`参数：

```javascript
fetch('https://example.com', {
  credentials: 'omit'  
})
```

### 3.1.3.检查fetch是否成功

一个`fetch()`的Promise将会Reject一个网络中遇到的[TypeError](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/TypeError)，尽管它通常表示近似于权限错误——404不会称为网络错误。一个成功的fetch\(\)的精确检查将包含Promise是否解决，然后检查Response中的ok属性是否为true，调用代码类似：

```javascript
fetch('flowers.jpg')
.then(function(response) {
  if(response.ok) {
    return response.blob();
  }
  throw new Error('Network response was not ok.');
})
.then(function(myBlob) { 
  var objectURL = URL.createObjectURL(myBlob); 
  myImage.src = objectURL; 
})
.catch(function(error) {
  console.log('There has been a problem with your fetch operation: ' + error.message);
});
```

#### 3.1.4.提供自己的请求对象

若您不想仅仅通过资源请求地址发送请求，您可以创建一个请求专用对象Request，然后将它传入到fetch\(\)方法中：

```javascript
var myHeaders = new Headers();

var myInit = { method: 'GET',
               headers: myHeaders,
               mode: 'cors',
               cache: 'default' };

var myRequest = new Request('flowers.jpg', myInit);

fetch(myRequest)
.then(function(response) {
  return response.blob();
})
.then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```

Request\(\)方法接收和fetch\(\)一样的参数，您同样可以将存在的Request对象传入而拷贝一个同样的对象：

```javascript
var anotherRequest = new Request(myRequest, myInit);
```

这个十分有用，因为Request和Response的正文都是一次性的，这样创建一个请求的副本允许您重新发送请求/响应，并且可验证初始化options。

_**NOTES**_：这里同样提供了一个`clone()`方法用于创建请求副本，但是它和拷贝方法具有不同的语义——若原始的正文已经被读取，则这种格式将会有问题。

### 3.2. Headers

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

#### 3.2.1. Guard

因为头信息可以在请求中发送，并且可以从响应中接收，所以这些信息会有一定的限制，为了不更改Header的信息，所以提供了和它相关的Guard属性。这个属性不是对Web应用开放的，但它限制了是否可更改Header的一些行为。

可用的Guard值包括：

* none：默认值
* request：从一个Request中可读取的Guard信息（[Request.headers](https://developer.mozilla.org/en-US/docs/Web/API/Request/headers)）
* request-no-cors：使用了[Request.mode](https://developer.mozilla.org/en-US/docs/Web/API/Request/mode) = no-cors创建的一个请求，并从该请求中读取Header的Guard信息
* response：直接从响应中读取的Header对应Guard信息（[Response.headers](https://developer.mozilla.org/en-US/docs/Web/API/Response/headers)）
* immutable：大部分时间用于ServiceWorkers，将Header处理成只读。

_**NOTES**_：对于Header值，您不可追加或者设置`Content-Length`，类似的，在Response头信息中添加`Set-Cookie`也是不允许的，ServiceWorkers同样也不允许在响应中同步设置`Set-Cookie`。

### 3.3. Response





