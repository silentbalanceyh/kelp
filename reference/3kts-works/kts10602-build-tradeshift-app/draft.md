## 2. App Authentication

大部分应用都需要和Tradeshift的API进行交互，若要启用它，Tradeshift实现了标准的OAUTH2协议（[RFC6749](https://tools.ietf.org/html/rfc6749)），对于APP，我们使用OAUTH中的3-legged协议，当激活这个应用时，用户将被要求当前账号进行认证和授权保证账号有权限做这些。当创建/编辑一个App时，您可以设置App的secret、client identifier，另外您需要选择您所访问的资源地址（OAUTH域中）。

### 2.1. OAuth 2 Authentication Sequence

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

* Authorization: 使用Basic认证，参数值通过Base64的方式加密`username:password`，用户名一般是



