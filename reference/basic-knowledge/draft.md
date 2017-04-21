## 5. Body

[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)中的Body的mixin用来描述请求/响应的正文，它允许您定义内容的媒体类型，并定义如何处理。Body部分同样被Request和Response实现了——它提供了这些对象相关的Associated的正文，一个很有用的标记。

### Properties

**Body.bodyUsed**：只读属性，返回一个Boolean值告诉客户端是否已经读取过Body了。

```javascript
var myBodyUsed = response.bodyUsed;
```

HTML Content

```html
<img class="my-image" src="https://wikipedia.org/static/images/project-logos/frwiki-1.5x.png">
```

JS Content

```javascript
var myImage = document.querySelector('.my-image');
fetch('https://upload.wikimedia.org/wikipedia/commons/7/77/Delete_key1.jpg').then(function(response) {
    console.log(response.bodyUsed);
    var res = response.blob();
    console.log(response.bodyUsed);
    return res;
}).then(function(response) {
    var objectURL = URL.createObjectURL(response);
    myImage.src = objectURL;
});
```

### Methods

**Body.arrayBuffer\(\)**：读取Response流直到完成，它将返回一个处理了ArrayBuffer内容的Promise。

```javascript
response.arrayBuffer().then(function(buffer) {
  // do something with buffer
});
```

示例：

```javascript
function getData() {
  source = audioCtx.createBufferSource();

  var myRequest = new Request('viper.ogg');

  fetch(myRequest).then(function(response) {
    return response.arrayBuffer();
  }).then(function(buffer) {
    audioCtx.decodeAudioData(buffer, function(decodedData) {
      source.buffer = decodedData;
      source.connect(audioCtx.destination);
    });
  });
};

// wire up buttons to stop and play audio

play.onclick = function() {
  getData();
  source.start(0);
  play.setAttribute('disabled', 'disabled');
}
```

**Body.blob\(\)**：读取Response流直到完成，它将返回一个处理了Blob内容的Promise。

```javascript
response.blob().then(function(myBlob) {
  // do something with myBlob
});
```

示例：

```javascript
var myImage = document.querySelector('img');

var myRequest = new Request('flowers.jpg');

fetch(myRequest)
.then(function(response) {
  return response.blob();
})
.then(function(myBlob) {
  var objectURL = URL.createObjectURL(myBlob);
  myImage.src = objectURL;
});
```



