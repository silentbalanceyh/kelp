# UCA20002 - field.RemoteCombo

## 1. Tree Map

![](/_images/specs/field/field-002-01.JPG)

## 2. Attributes

共享配置说明参考：[SPC20001 - 共享配置说明](/environment/specifications/212fields/spc20001-shared-configuration.md)

* 顶层：cid, name, type = **"field.RemoteCombo"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

1. 当前组件专用配置
   1. **form**：定义当前字段最终所属的Form，最终变更值的时候会充填这个Form中的字段信息
   2. **monitor**：定义当前字段监控的值信息，字段更新条件主要包括：
      1. monitor部分的数据发生了变化
      2. current部分的数据没有加载的时候，即当前值在undefined的时候
2. 可选配置
   1. **expr**：可选，display部分是否可支持表达式类型，如果可支持表达式类型则expr为true，这个属性可用于大部分组件，并不是当前组件专用
      ```
      "ajouter":{
          "label":"房价码",
          "value":"uniqueId",
          "expr":true,
          "display":"￥{price} - {marker}",
          "valueType":"number",
          ...
      }
      ...
      ```
   2. **valueType**：可选，对于特殊的一些Id，默认为String类型，如果执行JS中的比较时需要设置valueType，支持的值为number和string，目前主要是ID为数值的字段需要该类型
   3. **value/display**：下拉专用配置，用于设置字段信息

## 3. Remote Ingest

Remote专用配置（ingest节点）

1. **uri**：远程的uri信息
2. **input**：输入的参数信息，该参数信息对应路径以monitor中的数据为主
3. **method**：默认GET，可选配置，远程API的HTTP方法

**关于ingest的特殊说明**

```
   "ajouter":{
       "monitor":{
           "roomTypeId":["form","fmRoomForm","values","roomTypeId"]
       },
       "form":"fmRoomForm"
       ...
   },
   "ingest":{
       "uri":"/htl/code-room/q/type/:roomTypeId",
       "input":{
           "roomTypeId":["monitor","roomTypeId"]
       }
   }
```

注意远程配置中的对应关系，传递关系为：**Redux State -&gt; Monitor -&gt; Input**，也就是说上述配置可以按照以下片段配置：

```
   "ajouter":{
       "monitor":{
           "monitorField":["form","fmRoomForm","values","reduxField"]
       }
       ...
   },
   "ingest":{
       "input":{
           "inputField":["monitor","monitorField"]
       }
   }
```

* reduxField：状态树上的字段信息；
* monitorField：监控的字段信息，该字段信息可直接引用
* inputField：远程ingest的参数名称信息；



