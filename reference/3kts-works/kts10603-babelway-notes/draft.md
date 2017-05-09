## 3. Chapter 4

在Babelway中，消息是通过Channel来处理的，一个Channel定义了消息在Babelway中的全部执行路径。

核心组件包括：

* Gateway In：从外部系统到Babelway的入口；
* Message In：用于描述Incoming的消息结构；
* Transformation：描述了Incoming消息如何转换成Outcoming消息；
* Message Out：描述了Outcoming的消息结构；
* Gateway Out：描述了Babelway和外部系统交互的出口；

可选组件包括：

* Test Case：用于测试Channel是否配置正确，并生成期望结果；
* Notification：消息处理完成或者有错误时发送Email；
* Routing：用于描述同一个Gateway中对应多个Channel时，Channel应该执行哪一条消息；

### 3.1. Channel List

Next Deployment：

* No Change——Channel在下一次Deploy中将不会受影响；
* Start——Channel没有Deploy，但是将会在下一次Deploy中启动；
* Stop——Channel已经Deploy了，但是将在下一次Deploy中撤销（Undeploy）；
* Apply Changes——Channel目前是激活的，下一次Deploy将会使改变生效；





