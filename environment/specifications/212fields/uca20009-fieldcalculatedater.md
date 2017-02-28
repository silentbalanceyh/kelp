# UCA20009 - field.CalculateDater

## 1. Tree Map

![](/_images/specs/field/field-009-01.JPG)

## 2. Attributes

共享配置说明参考：[SPC20001 - 共享配置说明](/environment/specifications/212fields/spc20001-shared-configuration.md)

* 顶层：cid, name, type = **"field.CalculateDater"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

```json
"ajouter":{
    ...
    "format":{
        "date":"YYYY-MM-DD",
        "time":"HH:mm"
    },
    "monitor":{
        "arriveTime":["form","fmRoomForm","values","arriveTime"],
        "insideDays":["form","fmRoomForm","values","insideDays"]
    },
    "calculate":{
        "start":"arriveTime",
        "duration":"insideDays"
    },
    "refresher":["arriveTime","insideDays"],
    "datefield":["arriveTime"],
    "mode":"days"
}
```

* **format**：当前时间控件专用，设置日期部分和事件部分格式
* **monitor**：计算目标时间所需要的字段信息
* **calculate**：计算因子，使用开始时间字段和结束事件字段计算当前事件
* **mode**：决定了duration的模式，单位信息：年、月、日、时、分、秒、毫秒
* **refresher**：当monitor的值读取过后（监控值不为undefined），如果需要执行更新，则检查是基于refresher中的字段进行更新还是直接基于monitor中的字段进行更新，默认使用monitor中字段更新
* **datefield**：表示monitor中的字段哪些是时间格式字段，时间格式使用moment.js执行运算



