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

Vert.X专用配置有两个：

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

## 2. Restful Api专用配置

Restful Api专用配置主要有四个：

```
Route - <ROOT>/vertx/route/*.json
Uri - <ROOT>/vertx/uri/<两级目录>/data.json
Script
  - <ROOT>/vertx/uri/<两级目录>/script/in.json                ## in脚本配置
  - <ROOT>/vertx/uri/<两级目录>/script/<in-name>.js           ## in脚本文件
  - <ROOT>/vertx/uri/<两级目录>/script/out.json               ## out脚本配置
  - <ROOT>/vertx/uri/<两级目录>/script/<out-name>.js          ## out脚本文件
Rule - <ROOT>/vertx/uri/<两级目录>/rules/*.json
```

| 模型名 | 配置属性 | 备注 | 默认值 |
| :--- | :--- | :--- | :--- |
| PEScript | namespace | 脚本名空间 | （必填） |
|  | name | 脚本名称 | （必填） |
|  | content | 当前目录的JS文件名 | 不填则文件名为&lt;name&gt;.js |
| PERule |  |  |  |



