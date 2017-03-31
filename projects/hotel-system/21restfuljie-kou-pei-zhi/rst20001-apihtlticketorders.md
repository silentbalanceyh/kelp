# RST20001 - /api/htl/ticket/orders

## 1. 配置文件清单

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

## 3. 请求数据示例



