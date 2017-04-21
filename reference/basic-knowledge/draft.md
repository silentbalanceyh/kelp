请求和响应都可能包含正文数据（Body Data），一个正文数据可以是下边这些对象的实例：

* [ArrayBuffer](https://developer.mozilla.org/en-US/docs/Web/API/ArrayBuffer)
* [ArrayBufferView](https://developer.mozilla.org/en-US/docs/Web/API/ArrayBufferView)（Uint8Array、更友好）
* [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)/文件
* String（字符串）
* [URLSearchParams](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)
* [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)

正文的mixin包也定义了一些从正文中抽取数据的方法（这些方法同样被Request和Response对象实现了），它们都会返回一个可处理（Resolve）实际内容的Promise

* [arrayBuffer\(\)](/arrayBuffer%28%29)
* [blob\(\)](https://developer.mozilla.org/en-US/docs/Web/API/Body/blob)
* [json\(\)](https://developer.mozilla.org/en-US/docs/Web/API/Body/json)
* [text\(\)](https://developer.mozilla.org/en-US/docs/Web/API/Body/text)
* [formData\(\)](https://developer.mozilla.org/en-US/docs/Web/API/Body/formData)

这些方法可以让您使用非文本型数据，而且处理这些数据比XHR简单。

请求正文可以通过Body对应参数来设置：

```javascript
var form = new FormData(document.getElementById('login-form'));
fetch("/login", {
  method: "POST",
  body: form
});
```





