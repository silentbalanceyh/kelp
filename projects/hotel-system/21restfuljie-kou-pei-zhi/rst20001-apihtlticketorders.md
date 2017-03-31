# RST20001 - /api/htl/ticket/orders

## 1. 配置文件清单

```
1. Vert.X路由配置
 - /src/main/resources/deploy/htl/vertx/route/api.htl.orders.json
2. Api接口配置
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/data.json
3. 转换器和验证器
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/rules/c-replaced.json
4. 脚本引擎配置
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/script/in.json
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/script/in.api.htl.ticket.orders.post.js
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/script/out.json
 - /src/main/resources/deploy/htl/vertx/uri/Htl-PreOrder/post.htl.order/script/out.api.htl.ticket.orders.post.js
```

## 2. 接口配置

| 基本信息 | 值 | 备注 |
| :--- | :--- | :--- |
| Restful地址 | /api/htl/ticket/orders | 团队预定/散客预定统一接口 |
| 模型ID和数据表 | htl.orders | 对应数据表HTL\_ORDERS |
| HTTP方法 | POST |  |
| 脚本引擎 | in.api.htl.ticket.orders.post |  |
|  | out.api.htl.ticket.orders.post |  |
| 事件总线地址 | MSG://MESSAGE/QUEUE/DATA |  |
| 必须参数 | preservor | 预订人 |
|  | mobile | 联系电话 |
|  | orderItems | Array - 订单项，来源于子表单 |
| 转换器/验证器 | member | 会员卡号 -&gt; 会员ID |
|  | partner | 协议单位名称 -&gt; 协议单位ID |

## 3. 接口逻辑说明（按流程）

### 接口逻辑

1. member参数被自动转换成会员ID
2. partner参数被自动转换成协议单位ID

### 前置脚本

1. 根据member参数：关联存在会员/创建新会员
2. 根据partner参数：关联已经存在的协议单位
3. 根据orderItems参数计算订单项信息
4. OOB接口创建基订单
5. 根据基订单ID批量创建订单项
6. 【接口主逻辑】创建HTL系统订单：HTL\_ORDER

### 脚本共享变量

* `__PRICE`：订单总价格
* `__ORDER`：创建的基订单数据
* `__COROOM`：使用过的房价码（防止Out脚本重复读取）
* `__OITEM`：批量创建的订单项信息

### 后置脚本

1. 根据HTL\_ORDER创建的订单批量创建HTL系统订单项

## 4. 常量表

| 类型 | 表 | 值 | 说明 |
| :--- | :--- | :--- | :--- |
| member.type | Tabular | Registered | 创建会员流程——会员类型 |
| member.status | Tabular | New | 创建会员流程——会员状态 |
| order.status | Tabular | Ordered | 预订单初始化状态 |
| order.type | HCat | Preordain | 订单类型 |
| order.item.type | HCat | Preordain | 订单项类型 |
|  | Serial | Member | 会员编号 |
|  | Serial | PreordainItem | 订单项编号 |
|  | Serial | Preordain | 订单编号 |
|  | Serial | PreordainCredence | 订单凭证编号 |

## 5. 请求数据示例

```
{
  "identifier" : "htl.orders",
  "method" : "POST",
  "script" : {
    "in" : "in.api.htl.ticket.orders.post",
    "out" : "out.api.htl.ticket.orders.post"
  },
  "query" : { },
  "data" : {
    "hotelId" : 1,
    "companyId" : 1,
    "sigma" : "ENhwBAJPZuSgIAE5EDakR6yrIQbOoOPq",
    "preservor" : "裴元庆",
    "mobile" : 33333333,
    "partner" : "",
    "member" : "CD10000105",
    "amount" : 200,
    "subscribe" : "",
    "method" : 121,
    "codeGroup" : 5,
    "codeSource" : 15,
    "codeMarket" : 7,
    "codeMoney" : 1,
    "comment" : "",
    "orderItems" : [ {
      "roomCounter" : "2",
      "insidePersons" : "",
      "brekker" : "on",
      "leaveTime" : "2017-01-20T13:18:00.000Z",
      "arriveTime" : "2017-01-18T13:18:43.711Z",
      "sigma" : "ENhwBAJPZuSgIAE5EDakR6yrIQbOoOPq",
      "codeRoom" : 34,
      "roomTypeId" : "1",
      "insideDays" : "2",
      "pos" : "on",
      "divide" : "on"
    }, {
      "sigma" : "ENhwBAJPZuSgIAE5EDakR6yrIQbOoOPq",
      "roomTypeId" : "2",
      "codeRoom" : "55",
      "insideDays" : "4",
      "leaveTime" : "2017-01-22T13:19:00.000Z",
      "roomCounter" : "2",
      "insidePersons" : "",
      "brekker" : "on",
      "pos" : "on",
      "divide" : "on",
      "arriveTime" : "2017-01-18T13:19:31.374Z"
    } ],
    "sig" : "12065B409F10CA1C49AE09D1185F368830F573AA61C1FD4F09A \n
      1E6B087147B0B83636121F0E1F7CD94440D84C1DFEFBA7602AA1743C67D0A21E058E96ED5CBB8",
    "$$USER$$" : "87655095-15A0-412E-A887-1660040845FA"
}
```



