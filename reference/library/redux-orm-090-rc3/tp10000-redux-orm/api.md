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



