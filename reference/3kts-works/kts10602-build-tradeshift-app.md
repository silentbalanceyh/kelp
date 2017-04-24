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

一旦Tradeshift审批了这个应用，则它将在AppStore中可用并且可以激活。如果您在Sandbox中（sandbox.tradeshift.com）测试该应用，则您可以在生产环境部署它，接下来让Tradeshift做最终的Review，为了保证一致性，尽可能在两个环境中保持VendorId的一致性。

### 1.4. Full App Examples

Tradeshift创建了许多应用的例子让您更容易去理解这个架构，包括如何针对Tradeshift服务器授权，其中一个例子使用了Spring Boot、Node.JS、PHP，您可以在[GitHub](https://github.com/Tradeshift/tradeshift-app-samples)中查看。这里有[12分钟](https://drive.google.com/file/d/0Bx2z3BvoWzgtU05QdFludEROZ2c/view)视频演示了如何发布Spring.Boot应用以及如何配置成和Tradeshift这边协同工作。我们强烈推荐您完成这些简单联系来熟悉它，简单应用——Java Spring Boot演示了Tradeshift的Platform中不同的方面。

**Development Tips**

在开发过程，您往往需要迭代您的应用，并且Tradeshift服务器需要传递authentication tokens，我们推荐您使用[ngrok](https://ngrok.com/)或[localtunnel](https://localtunnel.github.io/www/)，允许将您的本地应用开放到公网上。

## 2. App Authentication

大部分应用都需要和Tradeshift的API进行交互，若要启用它，Tradeshift实现了标准的OAUTH2协议（[RFC6749](https://tools.ietf.org/html/rfc6749)），对于APP，我们使用OAUTH中的3-legged协议，当激活这个应用时，用户将被要求当前账号进行认证和授权保证账号有权限做这些。当创建/编辑一个App时，您可以设置App的secret、client identifier，另外您需要选择您所访问的资源地址（OAUTH域中）。

### OAuth 2 Authentication Sequence

**Step 1 - 重定向到Authorization Server获取授权码（Auth Code）**

App Vendor会提供Main URL和Redirect URL，这样Tradeshift服务器就知道通过何种顺序和谁进行通信，Main URL将会重定向下边的URL（TS授权服务器）来获取授权码：

```
https://api-sandbox.tradeshift.com/tradeshift/auth/login?response_type=code&
client_id={vendor_id.app_id}&redirect_uri={redirect_uri}&scope=offline
```

Tradeshift授权服务器将会发送下边的Http响应：

```
HTTP/1.1 302 Found
Location: {redirect_uri}?state&code={auth_code}
Example：
vendor_id = Eltec; app_id = MapApp
Redirect URL = https://appvendor.com/tradeshift/redirect
```

当用户打开一个iFrame应用，App Vendor的Main URL将会重定向到下边地址：

```
https://api-sandbox.tradeshift.com/tradeshift/auth/login?response_type=code&
client_id=Eltec.MapApp&redirect_uri=https://appvendor.com/tradeshift/redirect&scope=offline
```

之后Tradeshift授权服务器将会发送下边Http响应：

```
HTTP/1.1 302 Found
Location: https://appvendor.com/redirect?state&code=sE7RFd48QkijbjZ
```

**Step 2 - 通过授权码（Auth Code）交换令牌（Access Token）**

App Vendor将会创建一个请求发送到Token Endpoint：

```
POST: https://api-sandbox.tradeshift.com/tradeshift/auth/token
```

* Authorization: 使用Basic认证，参数值通过Base64的方式加密`username:password`，username的格式如：`{vendor_id.app_id}`，密码则是OAUTH2中在Developer设置的；
* Body内容类型：`application/x-www-form-urlencoded`
* 请求中发送的POST正文的参数编码使用`UTF-8`
* 参数`grant_type`：值必须是`authorization_code`
* 参数`code`：先前从应用中获取的授权码

该接口响应信息包括下边内容：

* `access_token`：用于访问API资源接口的令牌（Token）
* `token_type`：值为`Bearer`（通用的一种OAuth）
* `expires_in`：值为30，表示30分钟
* `id_token`：一个JSON格式的Web Token（JWT）——它包含大多数断言、以及用户的ID、用户所属的账号，只是需要解码。对于Web解码器，参考：[http://jwt.calebb.net/](http://jwt.calebb.net/) ，关于JWT的更多细节参考[https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32](https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32)。
* `client_id`：默认的App Vendor ID和App ID
* `refresh_token`：在Access Token过期后，这个刷新令牌可以用来获取新的Token，新的Access Token将在10分钟后过期，而Refresh Token则永远不会过期，可以使用多次。

**Example Request**

* 应用ID全名：Eltec.MapApp
* 客户端Secret：test123（可直接在Developer中设置）
* 使用Base64方式编码：`Eltec.MapApp:test123`（使用Basic认证）

```
POST: https://api-sandbox.tradeshift.com/tradeshift/auth/token
Authorization=Basic RWx0ZWMuTWFwQXBwOnRlc3QxMjM=&grant_type=authorization_code&code=sE7RFd48QkijbjZE
```

**Example Response**（包含Access、Id、Refresh Token）

```json
{
   "access_token": "EQ90um+/ODi7Gf1E98CSXNIhIVUY8w96n+6vKf5JtWwr2awGQoACND0XCVOaeza+HNocg10QujGnw6VlxMng3z7eVe6RCFAlGayFD4p3wVvaWzQKECwRoVxFjwcX3XwOcwfE1tT1MTAHGKb435VUaIN7peD9zo6L5SbdTuX5jNZzz4GWiZjdDo7iWVZQ0HmB/HzrIi6goTIohazOUJepAEZWV8koHrMwpqJFaKAaFJDgecJMREm18eaXhZ55Un2L6wwPOqF3KPV0dj/7ycNVRPlWrUC6M1oVlH4zsrdEGVMvN6ccpnD3pcDskQwLNtmos8srCXvy7doMsKmm1tliB8hmrzh/P9Eywjw8xSKeiW0wWGpe/oYLEgL10loqVGUn1vGRBRR5GUjIs+ysBVAAWgIAAQ==",
   "token_type": "Bearer",
   "expires_in": 30,
   "id_token": "eyJhbGciOiJub25lIn0.eyJleHAiOjE0MzYyMjYyNzAsInN1YiI6InNtYStzYXJhaGJyb3duQHRyYWRlc2hpZnQuY29tIiwidXNlcklkIjoiMjFkMjVjOTItYzBmNy00NGZkLWJiMzgtMzhiZjZmYmE3NDBmIiwiYXVkIjpbIkFCQ0luYy5NYXBBcHAiXSwiaXNzIjoiaHR0cHM6XC9cL2FwaS1zYW5kYm94LnRyYWRlc2hpZnQuY29tXC90cmFkZXNoaWZ0XC8iLCJqdGkiOiJqMmFyQXhRNHJOMFVVQm1qIiwiY29tcGFueUlkIjoiMDZhY2Q5MmItNmNiNS00OWZlLWFmZWUtOWY3YTBmZjMxODU1IiwiaWF0IjoxNDM2MjI2MjQwfQ.",
   "client_id": "ABCInc.MapApp",
   "refresh_token": "EQ90um+/ODi7Gf1E98CSXNIhIVUY8w96n+6vKf5JtWwr2awGQoACS8+RJkINGl1T50JQSVbLfxNhMPYb50Wv/t1ULeNdiPHrVqU1r/1wkjw56M1xhYSkkrXJsS25KCfHecV8lbrCt9d80ZASR6QrHd1O5/gWQ3Hzg09xCefVLm2Apq1ZRihWUIx2CEQU6SR+0U6cNbbtY7JdW/iwhD2ygPW40deguOrqYHtwZKqG/vSR1InWMBEzRNC2EZmSKkAfx+qoQa8ZDGcLRMvn3d4Jqc2W57YNzpOSTu/z8+Yeiob1Eeg3Ocse44yivDEkv9N82AgLSxpkQgWhZglkh+OgSnFU0Lt5dvQl/KuRb8+CgdFAg6usaUU+NYf/31pp68kZb5G4+7bxcUjAjPG7BVADWgIAAQ=="
}
```

## 3. Passing Tradeshift User Identity to Apps

`id_token`：描述了标准的JWT实现，而JWT是在[RFC7519](https://www.rfc-editor.org/rfc/rfc7519.txt)中实现的，JWT的Token中包含了所有App在运行上下文环境中可理解的一切。如果这个App和系统做集成则它将拥有自己的用户控件，也鼓励用户在Tradeshift用户ID和内部用户ID之间定义一个映射，这种方式中App自己可以为当前用户渲染相关属性是[JWT](https://www.rfc-editor.org/rfc/rfc7519.txt)中实现的，JWT的Token中包含了所有App在运行上下文环境中可理解的一切。如果这个App和系统做集成则它将拥有自己的用户控件，也鼓励用户在Tradeshift用户ID和内部用户ID之间定义一个映射，这种方式中App自己可以为当前用户渲染相关属性。JWT\) Token是未签名的，也就是说 alg = none，不包含任何数字签名信息；尽管如此，因为它是从指定请求的响应中获取的，所以不需要针对当前token进行签名验证。

这是解码过后Tradeshift传入到客户端的数据：

```json
{
  alg: "none"
}.
{
  exp: 1436226270,
  sub: "sma+sarahbrown@tradeshift.com",
  userId: "21d25c92-c0f7-44fd-bb38-38bf6fba740f",
  aud: [
    "ABCInc.MapApp"
  ],
  iss: "https:\/\/api-sandbox.tradeshift.com\/tradeshift\/",
  jti: "j2arAxQ4rN0UUBmj",
  companyId: "xxxxxxxx-6cb5-49fe-afee-9f7a0ff31855",
  iat: 1436226240
}.
[signature]
```

上边的信息含义包括（大部分JWT，最后两个是Tradeshift指定）：

* **exp**：超时时间Expiration Time
* **sub**：标题（Subject）或者Tradeshift的用户Email
* **aud**：一般这里使用App Name，表示接收人（Audience）
* **iss**：发放者，或Server的URL用来发放JWT令牌
* **jti：**JWT唯一标识符
* **iat**：最后一次发放时间
* **companyId**：激活Tradeshift中应用的组织的ID
* **userId**：Tradeshift用户的UUID

### SSO

用户若登陆到Tradeshift，那么SSO需要在用户账号中配置，当应用调用时，该用户已经是登陆状态。初始化请求期间，应用程序本身可以使用常规的OAuth2流程找出运行用户，并获取Tradeshift中通过`id_token`解码过的JWT令牌，然后应用程序可适当设置当前会话和当前用户，包括是否需要连接到其他API。

