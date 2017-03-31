# UCA20010 - field.CounterBox

## 1. Tree Map

![](/_images/specs/field/field-010-01.JPG)

## 2. Attributes

共享配置说明参考：[SPC20001 - 共享配置说明](/environment/specifications/212fields/spc20001-shared-configuration.md)

* 顶层：cid, name, type = **"field.CounterBox"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

```
"ajouter":{
    ...
    "monitor":{
        "roomTypeId":["form","fmRoomForm", "values","roomTypeId"],
        "arriveTime":["form","fmRoomForm", "values","arriveTime"]
    },
    "mode":"days",
    "datefield":["arriveTime"],
    "add-on":{
        "hidden":"hdRoomCounter",
        "label":{
            "id":"lblRoomCounter",
            "style":{
                "marginLeft":"20px",
                "color":"red"
            }
        }
    },
    "maxlength":2
},
...
```

* **monitor**：当前组件需要使用的字段信息
* **mode**：字段时间使用的mode，这个值会统一设置，所以设置最低要求即可
* **add-on**：挂载组件设置，支持三个key：hidden、label、dropdown；分别表示三个不同的组件，这三个组件对应下边的output配置
* **maxlength**：数值框必须的属性，用于表示当前数值的位数

### 4. Remote

```
"ingest":{
    "uri":"/htl/room-etat/q/day",
    "input":{
        "day":["monitor","arriveTime"],
        "roomTypeId":["monitor","roomTypeId"],
        "hotelId":["selector","uniqueId"]
    },
    "method":"POST",
    "output":{
        "hdRoomCounter":"{value}",
        "lblRoomCounter":"余{value}间"
    }
}
```

\*：其他部分略，这里仅仅说明output，output对应的key为add-on中的id信息，不同组件使用不同的id值来表示。

