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
| 必须参数 |  |  |



