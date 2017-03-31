# UCA20008 - field.ListSelector

## 1. Tree Map



## 2. Attributes

共享配置说明参考：[UCA20001：共享配置说明](/engine/spec/component/field-shared.md)

* 顶层：cid, name, type = **"field.ListSelector"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

* **value/display**：筛选器在筛选过程中的值和显示信息
* **async**：开启异步验证器
* **pagination**：筛选器支持分页的页配置信息
* **search**：当前筛选器开启搜索功能时，设置搜索的字段信息
* **columns**：筛选器的DataTable数据表的配置信息，配置对应列信息

## 4. Remote Ingest

Remote专用配置（ingest节点）

1. **uri**：远程的uri信息
2. **input**：输入的参数信息，该参数信息对应路径以monitor中的数据为主
3. **method**：默认GET，可选配置，远程API的HTTP方法
4. **orders**：当前ListSelector分页远程API的默认排序字段（ListSelector本身不支持列排序）
5. **pager**：当前ListSelector每一页的分页记录数
6. **linker**：筛选对应记录信息过后需要填充的字段数据key = value
   key：当前ListSelector记录中的字段值  
   value：填充的Form中对应的字段的name信息

```
"ingest":{
    "uri":"/vip/cards/pager",
    "input":{
        "hotelId":["uniqueId"]
    },
    "orders":[{"number":"ASC"}],
    "pager":{
        "size":10
    },
    "linker":{
        "number":"member",
        "amount":"amount",
        "realname":"preservor",
        "mobile":"mobile"
    }
}
```



