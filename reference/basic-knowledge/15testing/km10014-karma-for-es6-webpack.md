# KM10014 - Karma搭建ES6语法单元测试环境

在前端开发中，测试通常被忽略，本文介绍如何搭建ES6语法基础上的Karma自动化测试框架。什么是Karma呢？参考：[KTS10105 - Karma Installing](/reference/3kts-works/31training/kts10105-karma-installing.md)，本文是安装Karma的升级版，主要是让Karma可支持ES6的语法，它是Google开发的。Karma本身会启动一个服务器，生成包含JS源代码和JS测试脚本的页面。——运行浏览器界面，并显示测试结果。

如果开启检测，则文件有修改，则执行以上过程。

## Karma安装配置

初始化项目结构

```
karma-example
| -- src
      | -- index.js
| -- test
| -- package.json
```



