# HTL30003 - 前台接待入住

## 1. 菜单

* 前台接待 -&gt; （选择空房间，右键）办理注入

## 2. 表单逻辑

### 2.1.主表单

* 协议单位：弹出框选择协议单位
* 会员卡号：弹出框选择会员卡号
  * 预定人自动填充
  * 联系电话自动填充
* 入住天数
  * 如果离店日期发生改变，重算入住天数
* 离店日期
  * 如果入住天数发生改变，重算离店日期

### 2.2.子表单

* 宾客档案：弹出框从历史宾客档案中选择入住宾客

## 3. Restful接口

[RST20002 - /api/htl/ticket/inoccup](/projects/hotel-system/21restfuljie-kou-pei-zhi/rst20002-apihtlticketinoccup.md)

