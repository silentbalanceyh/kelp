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

## 2.Layout

您需要在使用过程微调HTML中的meta\( name = viewport \)：

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Layout</title>
        <meta name="viewport" content="width=device-width"/>
    </head>
    <body>
        <header data-ts="TopBar"></header>
        <main data-ts="Main">
            <div data-ts="MainContent">
                <h1>Main content</h1>
            </div>
        </main>
    </body>
</html>
```

### TopBar

即使你没有计划要使用**TopBar**，一个页面也需要有一个TopBar，因为它通常会在移动布局中作为主导航呈现。

### Main

**Main**会在Body部分越界时（hidden）绘制滚动条，这个设置会让你使用绝对定位设置设备固定位置——在Mobile设备中iframe里是不可用的。

### Main Content

**MainContent**元素应该包含需要呈现的所有一切。

### Aside

若您想要使用Aside菜单，必须将它放到Main之外，参考下边代码：

```html
<main data-ts="Main">
    <h1>Main content</h1>
</main>
<aside ts-aside>
    <div ts-panel>
        <p>Aside content</p>
    </div>
</aside>
```

## 3. Usage

所有的组件在使用的时候都会在元素中添加**data-ts**属性，这个框架会去识别这个属性并附上对应的行为和风格。

```html
<aside data-ts="Aside"> ... </aside>
```

### Config Attributes

一些额外属性可直接在component中配置，根据约定，这些属性都使用**data-ts.**前缀（包括点），如**data-ts.open**属性用于打开Aside：

```html
<aside data-ts="Aside" data-ts.open="true"> ... </aside>
```

### JS Interface

这些组件也会暴露【expose】JavaScript API，如**Aside**的打开行为也可以通过调用某个属性来完成，您可通过调用**ts.ui.get**来实现。

```js
var myelement = document.querySelector('#myaside');
var component = ts.ui.get(myelement);
console.log(component); // [object ts.ui.AsideSpirit]
component.open();
```

### API-Only Components

Component中一些复杂的行为通常使用纯JavaScript API来处理，它不包含标记，若想要在下一个阶段去处理组件复杂行为就可以这样做。

```js
ts.ui.DatePicker({
    title: "Your Birthday",
    value: '1973-03-26',
}).open();
```

## 4. Basic Rules

UI Component遵循以下简单的基本规则：

* HTML属性：**data-ts**
* 所有HTML的属性都以**data-ts.**开始
* 所有HTML的属性都以**data-ts-**开始
* 所有CSS的类名都使用**ts-**开始
* 全局名空间对象使用**ts.ui**



