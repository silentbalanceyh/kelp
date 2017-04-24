## 4. Permissions

你的App若调用Tradeshift Api，则当激活该应用时，用户需要设置相关权限。在Developer应用中，您可以控制应用所需权限。当激活该应用时，用户或管理员将检查所需的权限，尽可能保证您仅仅启用您所需要的权限，下边加锁的权限则没有办法修改。——权限信息是映射到OAuth2的域中。

## 5. Webhooks

通常，嵌入的应用需要和Tradeshift进行交互，如：当一个发票【invoice】或采购订单【purchase order】被一个公司激活的应用接收过后，这个app也许需要回应——要么存储一个文档、要么执行其他行为。

Webhooks允许您和Tradeshift中的一些特殊的事件【Event】交互，当其中某一个事件发生时，我们叫发送一个HTTP POST请求到Webhook中配置的URL，而Webhook是在应用级别配置的，当它触发时，它们将根据公司安装时的一些参数发送信息，这个事件的正文包含了额外的信息：如文档已经收到等。您可以在Developer的App中配置Webhook，滑动到Webhook章节，点击Add，则需要填写Webhook的名称以及提供POST请求将发送的地址。

如（`AFTER_DOCUMENT_RECEIVE`）

```
POST?event=AFTER_DOCUMENT_RECEIVE&id={event uuid}&tsUserId={user uuid}&tsCompanyAccountId={account uuid}&tsUserLanguage={user language}
```

**Parameters**

* **event**：事件名称，如：`AFTER_DOCUMENT_RECEIVE`
* **id**：事件ID
* **tsUserLanguage**：用户使用语言，如：`en_UK`
* **tsUserId**：用户关联的ID——您将在创建用户账号时得到用户的ID
* **tsCompanyAccountId**：激活App的公司



