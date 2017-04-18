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

## 1.Usage

### 1.1.Declare Your Models

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

### 1.2. Register Models and Generate an Empty Database State

在模型中定义字段指定了数据库中的表结构，为了生成数据库结构的描述，您需要统一的地方来注册这些Model。一个ORM类的实例可以注册所有的Model以及生成针对该Model的Schema信息并且将这些信息映射到数据库中。通常您需要一个文件来导入一个单独的ORM实例（创建统一创建中心，单件模式）：

```javascript
// orm.js
import { ORM } from 'redux-orm';
import { Book, Author, Publisher } from './models';

const orm = new ORM();
orm.register(Book, Author, Publisher);

export default orm;
```

您同样可以在同一个文件中注册ORM实例的模型，并且全部Export出来。现在您注册了一个Model，接下来可以生成空数据库状态，当前这个对象是一个普通对象，嵌入的是一个JavaScript：

```javascript
// index.js

import orm from './orm';

const emptyDBState = orm.getEmptyState();
```

### 1.3. Applying Updates to the Database

当您有了一个数据库的状态，则您可以开启一个ORM会话并修改其值。ORM实例提供了一个`session`方法：传入一个数据库状态，返回一个Session实例。

```javascript
const session = orm.session(emptyDBState);
```

使用Session的已注册模型本身可以用session对象的属性访问：

```javascript
const Book = session.Book;
```

Model提供了一个接口用来查询/更新数据库状态：

```javascript
Book.withId(1).update({ name: 'Clean Code' });
Book.all().filter(book => book.name === 'Clean Code').delete();
Book.hasId(1)
// false
```

初始化数据状态是不会突变的，一个新的数据库状态以及更新可以反映在Session实例中：

```javascript
const updatedDBState = session.state;
```



