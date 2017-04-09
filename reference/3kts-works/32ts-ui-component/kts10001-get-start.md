# Get Started

## 1.Basic

Lastest Version: 7.2.0-alpha.1

若要使用该脚本，直接在页面中引用：

```html
<script src="//d5wfroyti11sa.cloudfront.net/prod/client/ts-7.2.0-alpha.1.min.js"></script>
```

UI组件在`DOMContentLoaded` 事件中初始化，在使用它之前确保所有的组件都已经执行过初始化了。

```js
ts.ui.ready(function() {
    var component = ts.ui.get(element);
    component.dosomething();
});
```

* 在`DOMContentLoaded`之前上边回调函数会延迟执行
* 在`DOMContentLoaded`之后上边回调函数会立即执行

将启动代码全部放到上述代码中：

```js
ts.ui.ready(function() {
	boostrap_everything();
});
```



