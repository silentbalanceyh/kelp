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

Vert.X专用配置有两个，表中R表示必须配置的属性：

```
Address - <ROOT>/vertx/address/*.json
Verticle - <ROOT>/vertx/verticle/*.json
```

| 模型名 | 配置属性 | 备注 | 默认值 |
| :--- | :--- | :--- | :--- |
| PEAddress | workClass |  | com.vie.un.vtc.DataWorker |
|  | consumerHandler |  | com.vie.un.flow.DataReplier |
|  | consumerAddr |  | MSG://MESSAGE/QUEUE/DATA |
| PEVerticle |  |  |  |



