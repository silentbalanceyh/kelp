# KTS10701 - UBL Price说明

[Reference](http://www.oioubl.info/documents/en/en/Guidelines/OIOUBL_GUIDE_PRICES.pdf)

本文档翻译自引用PDF文件，用于描述UBL中的Price和Quantity的使用方法。

## 1. UBL Classes & Elements

在UBL的行级别，下边的元素用于描述价格和数量信息：

* OrderLine
* InvoiceLine

在OrderLine元素中，主要涉及的结构如下（OrderLine/LineItem）：

* Quantity
* LineExtensionAmount
* Delivery / Quantity
* Item / PackQuantity
* Item / PackSizeNumeric
* Price / PriceAmount
* Price / BaseQuantity
* Price / OrderableUnitFactorRate

```xml
<cbc:OrderLine>
    <cbc:LineItem>
        <cbc:Quantity unitCode="LTR">10</cbc:Quantity>
        <cbc:LineExtensionAmount currencyID="CNY">120</cbc:LineExtensionAmount>
        <cbc:Delivery>
            <cbc:Quantity unitCode="LTR">20</cbc:Quantity>
        </cbc:Delivery>
        <cbc:Item>
            <cbc:PackQuantity unitCode="EA">30</cbc:PackQuantity>                
            <cbc:PackSizeNumeric>40</cbc:PackSizeNumeric>
        </cbc:Item>
        <cbc:Price>
            <cbc:PriceAmount currencyID="CNY">1</cbc:PriceAmount>
            <cbc:BaseQuantity unitCode="EA">9</cbc:BaseQuantity>
            <cbc:OrderableUnitFactorRate>1</cbc:OrderableUnitFactoryRate>
        </cbc:Price>
    </cbc:LineItem>
</cbc:OrderLine>
```

在InvoiceLine元素中，主要涉及的结构如下（InvoiceLine）：

* InvoicedQuantity
* LineExtensionAmount
* Delivery / Quantity
* Price / PriceAmount
* Price / BaseQuantity
* Price / OrderableUnitFactorRate

```xml
<cbc:InvoiceLine>
    <cbc:InvoicedQuantity unitCode="LTR">10</cbc:InvoicedQuantity>
    <cbc:LineExtensionAmount currencyID="CNY">120</cbc:LineExtensionAmount>
    <cbc:Delivery>
        <cbc:Quantity unitCode="LTR">20</cbc:Quantity>
    </cbc:Delivery>
    <cbc:Item>
        <cbc:PackQuantity unitCode="EA">30</cbc:PackQuantity>
        <cbc:PackSizeNumeric>40</cbc:PackSizeNumeric>
    </cbc:Item>
    <cbc:Price>
        <cbc:PriceAmount currencyID="CNY">1</cbc:PriceAmount>
        <cbc:BaseQuantity unitCode="EA">9</cbc:BaseQuantity>
        <cbc:OrderableUnitFactorRate>1</cbc:OrderableUnitFactoryRate>
    </cbc:Price>
</cbc:InvoiceLine>
```

## 2. Element Explanations

| UK-name | Use | Remarks |
| :--- | :--- | :--- |
| Quantity | 1 | 和单位对应的数量信息 |
| Quantity@unitCode |  | 数量对应的单位代码 |
| PackQuantity | 0..1 | Item中打包的数量信息，这个信息在PackSizeNumeric中有描述。 |
| PackQuantity@unitCode |  | 指定单位代码 |
| PackSizeNumeric | 0..1 | 打包时一个包的数量信息 |
| PriceAmount | 1 | 基于BaseQuantity的最终金额信息，浮点格式 |
| PriceAmount@currencyID |  | 价格对应的货币代码 |
| BaseQuantity | 0..1 | 基数数量信息 |
| BaseQuantity@unitCode |  | 指定BaseQuantity的单位代码 |
| OrderableUnitFactorRate | 0..1 | 针对BaseQuantity的计算因子 |



