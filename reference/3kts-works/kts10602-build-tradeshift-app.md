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

### 1.2. Creating the Simplest App

最简单的App创建只需要提供一个URL即可——应用本身不需要Tradeshift API认证、同样不需要任何OAUTH2的参数，直接在Main URL中填写：

[https://www.google.com/maps/embed?pb=!1m10!1m8! 1m3!1d12613.0080182887!2d-122.39574375!3d37.78413355!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sus!4v1433284539696](https://www.google.com/maps/embed?pb=!1m10!1m8! 1m3!1d12613.0080182887!2d-122.39574375!3d37.78413355!3m2!1i1024!2i768!4f13.1!5e0!3m2!1sen!2sus!4v1433284539696)

注意：Main URL中填写的应用实际上不是由Tradeshift托管，也就是说您需要将您的应用部署在自己的服务器上——这样既方便您的应用和Tradeshift做集成，同样也可以定义自己任意的应用结构。

### 1.3. Main URL Gotcha & X-Frame-Origin

一些开发框架会自动发送代码防止它们的页面被包含在一个IFrame中，它们将发送HTTP头`X-Frame-Origin: SAMEORIGIN`或`X-Frame-Origin: Deny`比如上边的Google Map应用，我们则是用了嵌入式代码。查看[RFC7034](https://tools.ietf.org/html/rfc7034)了解更多X-Frame-Origin头，这些信息可以直接从Developer Tool中看到。我们推荐您确保您的应用不会发送`X-Frame-Origin`头——它可以允许Tradeshift将您的应用嵌入到IFrame中，您可以指定`ALLOW-FROM`，但这个仅仅只被一些浏览器支持（Firefox/IE），如果是基于Chromium的浏览器则会报错。

_Releasing App Into Appstore_

当您创建您自己的应用时，实际上创建的是一个隐藏类型——这个应用不可以在AppStore中方法，但可以安装在自己的账号下进行测试。若要发布您的应用到AppStore中，您需要提交给Tradeshift进行Review，发邮件到[apps@tradeshift.com](mailto:apps@tradeshift.com)（带上VendorId和AppId），则我们可以Review并Unhide它，Tradeshift最终会将它分配到对应的类别中。

一旦Tradeshift审批了这个应用，则它将在AppStore中可用并且可以激活。如果您在Sandbox中（sandbox.tradeshift.com）测试该应用，则您可以在生产环境部署它，接下来让Tradeshift做最终的Review。

