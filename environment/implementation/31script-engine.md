# JavaScript Engine

## 0.Introduction

JavaScript引擎是在Vie服务端嵌入的用于定制Restful接口逻辑的组件，整个Vie的JavaScript引擎主要包含三部分内容：

1. 系统环境（包括系统提供的Class）
2. OOB专用包（和OOB表相关联的专用脚本库）
3. 应用运行时环境（和项目相关的应用脚本库）

### 0.1.Resources Path

引擎专用脚本位于下边路径：

```
/src/main/resources/engine/script/js/
```

目前引擎脚本目录结构如下：

![](/_images/impl/script-engine/js/js-001-01.JPG)

* `init.properties`：引擎初始化脚本的顺序配置文件，描述了目录中脚本的加载先后顺序。
* **api**：脚本引擎读写数据库专用的统一调用API。
* **system**：全局环境初始化专用环境库文件。
* **db**：【工具】数据库访问专用工具库。
* **util**：【工具】脚本引擎常用工具库。
* **tp**：【动态加载】第三方JavaScript库引用专用目录。
* **ext**：【动态加载】特殊应用扩展库，添加自定义脚本专用。
* **oob**：【动态加载】OOB专用脚本库。

## 1. Table Content





