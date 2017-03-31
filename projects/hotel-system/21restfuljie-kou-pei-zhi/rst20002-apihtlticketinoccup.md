# RST20002 - /api/htl/ticket/inoccup

## 1. 配置文件清单

```
根目录：/src/main/resources/deploy/htl

1. Vert.X路由配置
 - /vertx/route/api.htl.orders.json
2. Api接口配置
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/data.json
3. 转换器和验证器
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/rules/c-replaced.json
4. 脚本引擎配置
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/script/in.json
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/script/in.api.htl.ticket.inoccup.post.js
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/script/out.json
 - /vertx/uri/Htl-PreOrder/post.htl.inoccup/script/out.api.htl.ticket.inoccup.post.js
```

## 2. 接口配置

| 基本信息 | 值 | 备注 |
| :--- | :--- | :--- |
| Restful地址 | /api/htl/ticket/inoccup | 前台接待首页直接入住 |
| 模型ID和数据表 | htl.orders | 对应数据表HTL\_ORDERS |
| HTTP方法 | POST |  |
| 脚本引擎 | in.api.htl.ticket.inoccup.post.js |  |
|  | out.api.htl.ticket.inoccup.post.js |  |
| 事件总线地址 | MSG://MESSAGE/QUEUE/DATA |  |
| 必须参数 | preservor | 预订人 |
|  | phone | 联系电话（和入住区分使用phone） |
|  | traveler | 入住人信息 |
|  | leaveTime | 离店日期 |
| 转换器/验证器 | member | 会员卡号 -&gt; 会员ID |
|  | partner | 协议单位名称 -&gt; 协议单位ID |
|  | leaveTime | 时间格式化 |
|  | arriveTime | 时间格式化 |

## 3. 接口逻辑说明（按流程）

### 接口逻辑

1. member参数被自动转换成会员ID
2. partner参数被字段转换成协议单位ID

### 前置脚本

1. 根据member参数：关联存在会员/创建新会员
2. 根据partner参数：关联已经存在的协议单位
3. 根据traveler参数计算入住人信息
4. 如果入住人是从宾客档案中提取，则变更宾客状态
5. 如果入住人是新的宾客则直接添加宾客档案
6. OOB接口创建基订单
7. 根据基订单ID创建唯一订单项（这种预定不产生多订单项和排房记录）
8. 【接口主逻辑】创建HTL系统订单：HTL\_ORDER

### 脚本共享变量

* `__PRICE`：订单总价格
* `__ORDER`：创建的基订单数据
* `__COROOM`：使用过的房价码（防止Out脚本重复读取）
* `__OITEM`：批量创建的订单信息
* `__TRAVELER`：宾客信息用于生成入住记录

### 后置脚本

1. 根据HTL\_ORDER创建的订单创建HTL系统订单项
2. 根据创建的系统订单项生成入住记录HTL\_OCCUP，入住记录的生成根据宾客信息来处理
3. 更新房间对应的房态信息

## 4. 常量表

| 类型 | 表 | 值 | 说明 |
| :--- | :--- | :--- | :--- |
| member.type | Tabular | Registered | 创建会员流程——会员类型 |
| member.status | Tabular | New | 创建会员流程——会员状态 |
| order.status | Tabular | Ordered | 预订单初始化状态 |
| inoccup.status | Tabular | Registered | 入住记录 |
| traveler.status | Tabular | OnGoing/History | 宾客状态切换 |
| order.type | HCat | Preordain | 订单类型 |
| order.item.type | HCat | Preordain | 订单项类型 |
|  | Serial | Member | 会员编号 |
|  | Serial | PreordainItem | 订单项编号 |
|  | Serial | Preordain | 订单编号 |
|  | Serial | PreordainCredence | 订单凭证编号 |
|  | Serial | Inoccup | 入住记录编号 |
|  | Serial | TravelerHistory | 宾客档案编号 |

## 5. 请求数据示例

```json
{
  "identifier" : "htl.orders",
  "method" : "POST",
  "script" : {
    "in" : "in.api.htl.ticket.inoccup.post",
    "out" : "out.api.htl.ticket.inoccup.post"
  },
  "query" : { },
  "data" : {
    "cat" : "$TABULAR$:Personal=preorder.category",
    "hotelId" : 1,
    "companyId" : 1,
    "method" : 135,
    "codeGroup" : 5,
    "codeSource" : 15,
    "codeMarket" : 7,
    "codeMoney" : 1,
    "arriveTime" : "2017-04-01 06:09",
    "codeRoom" : 34,
    "member" : "E0B742FB-C614-4877-ADB4-AA26332E9B13",
    "amount" : 200,
    "preservor" : "宇文成都",
    "phone" : 22222222,
    "insideDays" : 2,
    "leaveTime" : "2017-04-03 06:09",
    "comment" : "测试直接入住",
    "traveler" : [ {
      "roomNumber" : "1301",
      "roomTypeId" : 1,
      "type" : 46,
      "secret" : false,
      "idcType" : 66,
      "mobile" : "15922611447",
      "nation" : "汉族",
      "realname" : "裴元庆",
      "nickname" : "霹雳火",
      "nativePlace" : "四川省",
      "idcNumber" : "500235198508170010",
      "tserial" : "THR00000010",
      "address" : "测试地址",
      "country" : "中国",
      "birthday" : "1985-10-17 00:00:00",
      "$INDEX$" : 0
    } ],
    "sigma" : "ENhwBAJPZuSgIAE5EDakR6yrIQbOoOPq",
    "sig" : "109E9ACBB30C1EA60C95309E9E476C4A985F26A6D9EEEE7A2D146506576EE93AEEB \n 
      A87FEDFBC40657D63296A4BF84942241F41496D6D52054A0460C8F423E96E",
    "$$USER$$" : "3FE1E35D-0B7D-48DA-8DCC-9E5438D7F5B0"
  }
}
```



