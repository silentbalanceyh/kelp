# KTS10602 - Tradeshift App Development

Tradeshift的应用主要包含两部分：Component UI和Restful Api

## 1. Creating Embedded App

Tradeshift维护了两套环境：sandbox.tradeshift.com和go.tradeshift.com，两套环境都是生产级环境。Sandbox可让您用于开发，而开发完成过后，可将您的环境Deploy到Go中。

### 1.1. Creating an App

创建应用之前，您需要一个Tradeshift的账号，您可以在主页中注册；一旦登陆过后，您可以到AppStore中去找到Developer应用，激活该应用时，它会让您填写一个Vendor的ID。所有Tradeshift中的应用都是使用VendorId.AppName的格式进行标识，如Tradeshift.Developer是您安装的应用，那么您就可以使用这个名称在您的生产环境中作为应用标示符——所以VendorId本身不允许任何点`.`符号。

一旦设置了VendorId，则您就可以直接创建您的应用，应用参数会在您的Developer App界面中，主要需要填写的参数如下：

* **APP ID**：系统自动生成（不可带点`.`符号）
* **APP NAME**：应用名称，主要用于显示
* **APP DESCRIPTION**：应用描述信息，主要用于介绍该应用
* **APP ICON**：应用本身的图标信息
* **DEFAULT LOCALE**：默认的时区信息、语言信息
* **OAUTH2 CLIENT ID**：OAuth集成专用的Client ID参数信息
* **OAUTH2 CLIENT SECRET**：OAuth集成专用的客户端secret信息
* **OAUTH2 REDIRECT URI**：OAuth专用的Callback的URI地址（注意区分Main URL）
* **PERMISSIONS**：一些支持的Tradeshift服务端API的权限
* **WEBHOOKS**：Web应用的一些钩子程序
* **MAIN URL**：应用的主界面地址





