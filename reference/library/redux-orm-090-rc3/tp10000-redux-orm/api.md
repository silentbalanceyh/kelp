### 4.4. QuerySet

您可以调用所有`Model`类中拥有的功能，因为它们都是`Model`的类方法，这种情况下`QuerySet`中一般是所有的`Model`实例。

**Instance Methods**：

* `toRefArray()`：返回`QuerySet`中用来描述的一个JavaScript对象的数组，这些对象会直接引用存储中的数据。
* `toModelArray()`：返回`QuerySet`中一个`Model`实例对象的数组。
* `count()`：返回`QuerySet`中`Model`实例的数量。
* `exists()`：如果包含实体数量大于0则返回true，反之返回false。
* `filter(filterArg)`：根据传入的filter从原始`QuerySet`中生成一个新的`QuerySet`。对于filterArg参数，您可以传入一个`redux-orm`相匹配的实体，或者传入一个自定义函数，返回true则将该实体放到新的`QuerySet`中，反之则排除。
* `exclude`：【略】，和`filter(filterArg)`功能相反。
* `all()`：返回一个`QuerySet`的新拷贝。
* `at(index)`：根据索引读取`QuerySet`对应位置上的`Model`实例。
* `first()`：读取第一个`Model`，index = 0。
* `last()`：读取最后一个`Model`，index = `querySet.count() - 1`。
* `delete()`：删除所有实体。
* `update(mergedObj)`：更新所有`QuerySet`中的实体，将传入的对象和这些实体一一合并。

### 4.5. Session

**Instantiation**：您不需要自己创建该对象，直接使用`orm.session`即可。

**Instance Properties**：state：当前数据库对应数据在会话中的状态。

另外，您可以访问所有Schema中注册过的Model，查询以及更新对应实例属性，如：

```javascript
const session = orm.session(state);
session.Book // Model class: Book
session.Author // Model class: Author
session.Book.create({id: 5, name: 'Refactoring', release_year: 1999});
```



