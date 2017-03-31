# DEV10001 - 不重启Engine发布脚本

\*：不支持OOB和HTL（应用系统）的跨接口脚本发布

## 1. 配置文件位置

![](/_images/impl/develop/dev-10001-01.JPG)

```
/src/main/resources/develop/script/meta.json
```

## 2. 配置文件内容

```
{
	"root":"deploy/htl/vertx/uri/",
	"path":[
		"Htl-PreOrder/post.htl.inoccup/"
	]
}
```

* **root**：发布脚本的Uri根路径，要么OOB要么应用系统根路径
* **path**：需要发布的脚本的Uri配置路径集合

## 3. 运行脚本

![](/_images/impl/develop/dev-10001-02.JPG)

```
/src/main/java/com/vie/dev/ScriptIssuer.java
```

## 4. 运行结果



