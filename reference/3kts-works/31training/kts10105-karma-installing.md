# KTS 10105 - Karma的安装

Jasmine是一个JS的测试工具，在Karma上运行Jasmine可完成JS的自动化测试、生成覆盖率报告等。本文不包含Jasmine的细节，主要介绍Karma的安装。

## 1. Karma介绍

Karma是Testacular的新名字，2012年Google开源了Testacular，2013年改名为Karma——它是一个让人感到很神秘的名字，表示佛教中的缘分，因果报应，比Cassandra这种名字更让人猜不透！它是一个基于Node.js的JavaScript测试执行过程管理工具（Test Runner），该工具可用于测试所有主流Web浏览器，也可集成到CI\(Continuous Integration）工具，也可和其他代码编辑器一起使用。这个测试工具的一个强大特征就是，它可以监控（Watch）文件的变化，然后自行执行，通过console.log显示测试结果。

Jasmine是单元测试框架，本文将介绍用Karma让Jasmine测试自动化完成，istanbul是一个单元测试代码覆盖率检查工具，可以很直观地告诉我们，单元测试对代码的控制程度。

## 2. Karma的安装

```bash
npm install -g karma
karma start
```

运行完成后就可以看到下边的输出：

```
19 04 2017 11:38:10.492:WARN [karma]: No captured browser, open http://localhost:9876/
19 04 2017 11:38:10.507:INFO [karma]: Karma v1.6.0 server started at http://0.0.0.0:9876/
```

## 3. Karma + Jasmine配置

最初需要初始化karma的配置文件：karma.conf.js





