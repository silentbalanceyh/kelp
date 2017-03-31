# Restful配置默认值

## 0. 对应关系

模型和数据表的对应关系如下

* PEAddress - EVX\_ADDRESS
* PERoute - EVX\_ROUTE
* PERule - EVX\_RULE
* PEUri - EVX\_URI
* PEVerticle - EVX\_VERTICLE
* PEScript - ENG\_SCRIPT

## 1. Vert.X专用配置

Vert.X专用配置有两个，表中R表示必须配置的属性，不配置的带有默认值：

```
Address - <ROOT>/vertx/address/*.json
Verticle - <ROOT>/vertx/verticle/*.json
```

| 模型名 | 配置属性 | 备注 | 默认值 |
| :--- | :--- | :--- | :--- |
| PEAddress | workClass | Worker对应Java类名 | com.vie.un.vtc.DataWorker |
|  | consumerHandler | Worker中处理相应Handler对应Java类名 | com.vie.un.flow.DataReplier |
|  | consumerAddr | EventBus中的消息地址 | MSG://MESSAGE/QUEUE/DATA |
| PEVerticle | instances | 实例数量 | 1 |
|  | name | Worker对应Java类名 | com.vie.un.vtc.DataWorker |
|  | group | Verticle分组信息 | \_\_DEFAULT\_\_ |
|  | jsonConfig | Verticle配置项 | {} |
|  | isolatedClasses | 分组后的独立类信息 | \[\] |
|  | extraCp | 分组后的独立类路径 | \[\] |
|  | ha | 支持HA？ | FALSE |
|  | worker | 是Worker实例？ | FALSE |
|  | multi | 是Multi Thread Worker？ | FALSE |



