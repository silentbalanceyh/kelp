# UCA40001 - List字段提交弹出框验证

## 1.Example

![](/_images/specs/op/op-001-01.png)

验证效果：

![](/_images/specs/op/op-001-02.JPG)

## 2.Tree Map

![](/_images/specs/op/op-001-03.JPG)

## 3.Comments

1. 按钮内容配置

   ```json
    # ui.op -> op.CommonButton
    ...
    "config":{
        // Optional
        "text":"预定",
        "adj":"blue",

        // Required
        "validate":{
            "orderItems":{
                "type":"LIST",
                "length":1,
                "message":"对不起，请填写预定的房型信息！"
            }
        }
    }
   ```

   * Optional：如果按钮使用连接模式，按钮本身隐藏，text和adj两个属性可以不配置；
   * Required：必须配置的关于验证的核心属性；
     * orderItems：验证的字段名
       * type：针对LIST验证为固定值：LIST
       * length：提交的时要求的LIST的最小长度
       * message：弹出框中显示的验证失败消息

## 4.Development

**Constant Values**

* LIST：类型只能为两个值，这种验证只能是LIST，可支持的值（LIST,OBJECT\)

**Code**

```
File：rtv/src/plot/form/Form.Submit.js （ validate函数 )
```



