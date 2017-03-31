# UCA20006 - field.TabularDrop

## 1. Tree Map

![](/engine/spec/component/img/field-007-01.JPG)

## 2. Attributes

共享配置说明参考：[SPC20001 - 共享配置说明](/environment/specifications/212fields/spc20001-shared-configuration.md)

* 顶层：cid, name, type = **"field.TabularDrop"**
* 提示类：label, hint, tips
* 必填/可选：asterisk = \( required/optional \)
* 图标/单位：icon, unit, position = \( left/right \)
* 布局信息：inline, fieldset, location

## 3. Special

* **code**：对应状态树上边的Tabular数据列表的code值

![](/engine/spec/component/img/field-007-02.png)

* **value/display**：下拉框中显示对应的字段以及值对应的字段信息

_\*：和_`field.RemoteCombo`_一样display可支持expr=true的呈现模式，即支持Expression的方式呈现对应信息。_

