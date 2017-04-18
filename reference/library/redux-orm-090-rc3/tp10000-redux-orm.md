# TP10001 - （redux-orm）官方基础

## Installation

安装该包有两种方式：npm方式、浏览器方式：

npm方式：

```
npm install redux-orm --save
```

浏览器方式：

```html
<script src="https://tommikaikkonen.github.io/redux-orm/dist/redux-orm.js"></script>
```

版本下载：

* 完整版：[https://tommikaikkonen.github.io/redux-orm/dist/redux-orm.js](https://tommikaikkonen.github.io/redux-orm/dist/redux-orm.js)
* 最小版：[https://tommikaikkonen.github.io/redux-orm/dist/redux-orm.min.js](https://tommikaikkonen.github.io/redux-orm/dist/redux-orm.min.js)

## Declare Your Models

您可使用ES6的语法定义模型【Model】，直接从`Model`继承过来，您需要定义模型的所有关联字段，所有数据字段推荐定义成带`Getter/Setter`的字段，它们可以直接在初始化Model时设置。`redux-orm`支持一对一、多对多的关系，它可使用外键（`oneToOne`，`many`和`fk`导入），普通属性访问方式和JavaScript对象一样。

```javascript
// models.js
import {fk, many, attr, Model} from 'redux-orm';

class Book extends Model {
    toString() {
        return `Book: ${this.name}`;
    }
    // Declare any static or instance methods you need.
}
Book.modelName = 'Book';

// Declare your related fields.
Book.fields = {
    id: attr(), // non-relational field for any value; optional but highly recommended
    name: attr(),
    authors: many('Author', 'books'),
    publisher: fk('Publisher', 'books'),
};
```



