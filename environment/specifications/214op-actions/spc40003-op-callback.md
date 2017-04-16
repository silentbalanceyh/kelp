# SPC40003 - 关于OP回调的配置

## 1.基础配置

OP的基础配置主要配在表`ui.op`中配置，配置在`callback`节点中，基本配置如下：

```json
"callback":{
    "DIALOG":{
        "success":"酒店信息更新成功，系统将重新加载酒店信息！"
    },
    "CACHE":{
        "key":{
            "api":"/htl/hotels/q/emp/{user.employeeId}",
            "method":"GET"
        }
    },
    "EXECUTE":["DIALOG","CACHE","FORMRELOAD"]
},
...
```

上边是其中一个配置片段，主要配置节点信息分为两类：

* 事件类型：如`DIALOG`，`CACHE`等都属于事件类型节点，节点中为当前事件所需要配置信息。
* `EXECUTE`节点：`EXECUTE`节点负责将事件组织起来，表示了事件执行顺序。

## 2.EXECUTE节点说明

EXECUTE节点配置的数据为Array类型，每一个元素都是`callback`节点中的事件类型，且包含了当前事件的配置信息。

