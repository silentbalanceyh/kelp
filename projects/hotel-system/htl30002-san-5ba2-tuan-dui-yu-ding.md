# HTL30002 - 散客/团队预定

## 1. 菜单

* 预定管理 -&gt; 散客预定
* 预定管理 -&gt; 团队预定

## 2.表单逻辑

### 2.1.主表单

* 协议单位：弹出框选择协议单位
* 会员卡号：弹出框选择会员卡号
  * 预订人自动填充
  * 联系电话自动填充

### 2.2. 子表单

* 房间类型：选择房间类型
  * 房价码根据房间选择类型刷新
* 抵达日期：选择抵达日期（时间为当前时间）
* 入住天数
  * 如果抵达日期和离店日期都填写了，自动计算入住天数
  * 变更抵达日期不重算入住天数（主逻辑：抵达日期 + 入住天数 = 离店日期）
  * 变更离店日期重算入住天数
* 离店日期
  * 如果抵达日期和入住天数都填写了，自动计算离店日期
  * 变更抵达日期或入住天数时重算离店日期
* 房间数
  * 如果房间类型和抵达日期都有值，则直接统计目前房型的可预订数量
  * 房间类型和抵达日期变更时，重算目前房型可预订数量

## 3. 接口说明

### 3.1. 配置文件清单

```
1. Vert.X路由配置
 - /src/main/resources/deploy/vertx/route/api.htl.orders.json
2. Api接口配置
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/data.json
3. 转换器和验证器
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/rules/c-replaced.json
4. 脚本引擎配置
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/script/in.json
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/script/in.api.htl.ticket.orders.post.js
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/script/out.json
 - /src/main/resources/deploy/vertx/uri/Htl-PreOrder/post.htl.order/script/out.api.htl.ticket.orders.post.js
```

### 3.2. 接口配置

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
|  |  |  |



