Previous：[TPL10001 - redux-orm](/reference/library/redux-orm-090-rc3/tp10000-redux-orm.md)

## 4. API

### 4.1. ORM

您可以查阅所有的ORM文档：[Document](http://tommikaikkonen.github.io/redux-orm/ORM.html)

初始化

```javascript
const orm = new ORM();
```

实例化方法：

* `register(...models: Array<Model>)`注册Model类到ORM实例；
* `session(state: any)`开启一个带状态`state`的会话`Session`。

### 4.2. Redux Integration

* `createReducer(orm: ORM)`：返回一个可以插入Redux的reducer函数，这个reducer将会返回数据库的下一个状态，调用它之前您只需要注册您的Model类。
* `createSelector(orm: ORM, [...inputSelectors], selectorFunc)`：返回一个可记忆的selector函数`selectorFunc`，它使用`session`作第一参数，接着使用任意`inputSelectors`作为第二参数。

### 4.3. Model

您可以查看Model详细文档。

**Instantiation**：不要直接创建Model，尽可能使用`create`创建。

**Class Methods：**

* `hasId(id)`：检查数据库中是否包含了当前id对应的状态记录。
* `withId(id)`：根据id读取Model的实例。
* `get(matchObj)`：根据匹配属性`matchObj`读取相匹配的Model实例。
* `create(props)`：可使用`props`创建一个新的Model，如果没有提供id，则新的id应该是`Math.max(...allOtherIds) + 1`。

**Instance Attributes**：

* `ref`：返回JavaScript对象的直接引用，用于描述Model实例。

**Instance Methods**：

* `equals(otherModel)`：判断当前Model和`otherModel`是否相等，相等表示所有的属性值都相等。
* `set(propertyName,value)`：更新`propertyName`属性的值为`value`，返回值是`Undefined`，等价于通常赋值语句。
* `update(mergeObj)`：将`mergeObj`中的数据合并到当前Model中，返回`Undefined`（该方法存在引用修改）。
* `delete()`：从数据库中删除当前Model实例记录，返回`Undefined`。

**Subclassing**

您可以直接使用ES6中的语法从`Model`实现继承，任何定义的实例方法在Model实例中都是有效的，而任何静态方法则是在Class类级别有效。针对关联字段定义，可设置类中的`fields`来实现，也可以像下边这种方式来实现：

```javascript
class Book extends Model {
    static get fields() {
        return {
            id: attr(),
            name: attr(),
            author: fk('Author'),
        };
    }
}
// alternative:
Book.fields = {
    id: attr(),
    name: attr(),
    author: fk('Author'),
}
```

所有的fk、oneToOne、many都接收一个参数（它关联的Model名），这些字段将作为每个Model实例上的属性提供，您可以使用相关实例的id值或相关实例本身设置该关联实例的字段。

对于fk，您可以通过`author.bookSet`访问相反的关系，其中相关名称为`${modelName}Set`，对许多人也是如此，对于`oneToOne`，只能通过字段上声明的模型名称来访问反向关系：`author.book`。

对于many，访问Model实例上的字段将会返回一个`QuerySet`，另外还有两个方法`add`和`remove`，它们获取一个或多个参数，其中参数是模型实例或其ID，调用这些方法将会返回存储中的记录的下一个状态。

声明模型类时，始终记住设置`modelName`，它必须被明确设置，并且勇于解析所有相关字段。

**Declaring `modelName`**：

```javascript
class Book extends Model {
    static get modelName() {
        return 'Book';
    }
}
// alternative:
Book.modelName = 'Book';
```

**Declaring `options`**：

若您想要针对`redux-orm`数据库指定选项，则您可以在Model类中定义一个静态`options`属性，通常使用id定义属性名表示键值。

```javascript
// This is the default value. 
Book.options = {
    idAttribute: 'id',
};
```



