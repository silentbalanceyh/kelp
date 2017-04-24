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

