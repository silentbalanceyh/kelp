# KTS10701 - UBL Price说明

[Reference](http://www.oioubl.info/documents/en/en/Guidelines/OIOUBL_GUIDE_PRICES.pdf)

本文档翻译自引用PDF文件，用于描述UBL中的Price和Quantity的使用方法。

## UBL Classes & Elements

在UBL的行级别，下边的元素用于描述价格和数量信息：

* OrderLine
* InvoiceLine

在OrderLine元素中，主要涉及的结构如下：

```xml
<Order>
    <cbc:OrderLine>
        <cbc:LineItem>
            <cbc:Quantity unitCode="LTR">10</cbc:Quantity>
            <cbc:LineExtensionAmount currencyID="CNY">120</cbc:LineExtensionAmount>
            <cbc:Delivery>
                <cbc:Quantity unitCode="LTR">11</cbc:Quantity>
            </cbc:Delivery>
            <cbc:Item>
                <cbc:PackQuantity></cbc:PackQuantity>
            </cbc:Item>
        </cbc:LineItem>
    </cbc:OrderLine>
</Order>
```



