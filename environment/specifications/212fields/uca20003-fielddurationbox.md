# UCA20003 - field.DurationBox

## 1. Tree Map

![](/engine/spec/component/img/field-009-01.JPG)

## 2. Attributes

共享配置说明参考：[UCA20001：共享配置说明](/engine/spec/component/field-shared.md)

* 顶层：cid, name, type = **"field.DurationBox"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

```
"ajouter":{
    ...
    "monitor":{
        "attriveTime":["form","fmRoomForm","values","attriveTime"],
        "leaveTime":["form","fmRoomRoom","values","leaveTime"]
    },
    "calculate":{
        "start":"arriveTime",
        "end":"leaveTime"
    },
    "refresher":["leaveTime"],
    "datefield":["arriveTime","leaveTime"],
    "pattern":"YYYY-MM-DD HH:mm",
    "mode":"days"
}
```

1. **monitor**：计算duration所需要的时间字段信息
2. **duration**：专用配置，仅包含from和to两个键值，from表示开始，to表示结束
3. **pattern**：解析时间格式用的pattern
4. **mode**：计算时用的维度，时间计算维度包括：年、月、日、时、分、秒
5. **refresher**：当monitor的值读取过后（监控值不为undefined），如果需要执行更新，则检查是基于refresher中的字段进行更新还是直接基于monitor中的字段进行更新，默认使用monitor中字段更新
6. **datefield**：表示monitor中的字段哪些是时间格式字段，时间格式使用moment.js执行运算



