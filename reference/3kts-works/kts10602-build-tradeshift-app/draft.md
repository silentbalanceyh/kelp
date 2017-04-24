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



