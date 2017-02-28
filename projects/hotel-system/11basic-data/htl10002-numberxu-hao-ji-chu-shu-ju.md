# Number List

## 1. Comments

系统编号表记录了系统中一些数据的编号规则，一般的编号规则分为下边几个部分

### 1.1. 基本序号

基本序号为生成序号的基本规则，规则中主要包含下边几种：

* decrement：默认false，序号是否递减；
* step：默认为1，序号的步进因子；
* current：默认为1，序号当前值；
* length：长度必填，如果current长度不满足则直接在左边填充0；

### 1.2. 完整序号

完整序号包含以下几个部分

* PREFIX：编号前缀，直接设置
* SUFFIX：序号后缀，直接设置
* TIME：序号中使用了时间戳部分${time}
* CURRENT：序号中的基本序号部分，变量使用${seed}

## 2. Table List

| 序号代码 | 备注 | 格式 | 示例 |
| :--- | :--- | :--- | :--- |
| CodeMarket | 市场码序号 | PREFIX + CURRENT\(3\) | MKC001 |
| CodeSource | 来源码序号 | PREFIX + CURRENT\(3\) | SRC001 |
| CodeShift | 班别码序号 | PREFIX + CURRENT\(3\) | DTC001 |
| CodeMoney | 佣金码序号 | PREFIX + CURRENT\(3\) | MMC001 |
| CodeGroup | 团购码序号 | PREFIX + CURRENT\(3\) | GPC001 |
| CodePrice | 房价码序号 | PREFIX + CURRENT\(3\) | RPC001 |
| Preordain | 预定订单序号 | PREFIX + CURRENT\(10\) | POD0000000001 |
| Preordainitem | 预定订单项序号 | PREFIX + CURRENT\(10\) | POI0000000001 |
| Member | 会员号 | PREFIX + CURRENT\(10\) | MBR0000000001 |
| Employee | 员工工号 | PREFIX + CURRENT\(10\) | EMP0000000001 |
| PreordainCredence | 订单凭证号 | PREFIX + ${time} + CURRENT\(10\) | FC20150203110000000001 |
| Inoccup | 入住记录序号 | PREFIX + CURRENT\(12\) | IOP000000000001 |
| Schedule | 排房记录序号 | PREFIX + CURRENT\(12\) | SCH000000000001 |
| AccountItem | 会计科目编号 | PREFIX + CURRENT\(10\) | AT0000000001 |
| PayBill | 账单编号 | PREFIX + CURRENT\(16\) | PB0000000000000001 |
| PriceType | 价格类型 | PREFIX + CURRENT\(3\) | PT001 |
| PreOrderMethod | 预定方式 | PREFIX + CURRENT\(3\) | ODM001 |
| PreOrderHCat | 预定类型 | PREFIX + CURRENT\(3\) | ODC001 |
| TravelerHistory | 宾客档案 | PREFIX + CURRENT\(8\) | THR00000001 |



