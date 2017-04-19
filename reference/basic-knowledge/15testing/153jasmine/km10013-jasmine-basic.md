# KM10013 - Jasmine基础知识

TDD（Test Driven Development）测试驱动开发是敏捷开发中提出的最佳实践之一。jasmine很有意思地提出了BDD（Behavior Driven Development）行为驱动开发。测试驱动开发，对软件质量起了规范性的控制——未写实现，先写测试，一度成为Java领域中研发的圣经。随着JS兴起，功能越来越多，代码量越来越大，开发人员素质相差悬殊，真的有必要建立对代码的规范性控制。Jasmine就是为团队合作而生。

## 1. Jasmine介绍

Jasmine是一个用来编写JS测试的框架，它不依赖于任何其他的JS框架，也不需要对DOM，它拥有灵巧而明确的语法可以让您轻松编写测试代码。Jasmine本身结构很简单：

```javascript
describe("A suite", function() {
  var foo;
  beforeEach(function() {
    foo = 0;
    foo += 1;
  });

  afterEach(function() {
    foo = 0;
  });

  it("contains spec with an expectation", function() {
    expect(true).toBe(true);
  });
});
```

每个测试都在一个测试集中运行，Suite就是一个测试集，用describe函数封装，Spec表示每个测试用例，用it函数封装。通过expect函数，作为程序断言来判断相等关系。setup过程用beforeEach函数封装，tearDown过程用afterEach封装。

## 2. Jasmine安装

安装jasmine类库时，可直接使用bower一键搞定。

```
~ D:\workspace\javascript>mkdir jasmine
~ D:\workspace\javascript>cd jasmine

#查找jasmine项目
~ D:\workspace\javascript\jasmine>bower search jasmine
-----------------------------------------
Update available: 1.2.4 (current: 1.1.2)
Run npm update -g bower to update
-----------------------------------------

Search results:

    jasmine git://github.com/pivotal/jasmine.git
    jasmine-jquery git://github.com/velesin/jasmine-jquery
    jasmine-sinon git://github.com/froots/jasmine-sinon.git
    jasmine-ajax git://github.com/pivotal/jasmine-ajax.git
    jasmine.async git://github.com/derickbailey/jasmine.async.git
    flight-jasmine git://github.com/twitter/flight-jasmine.git
    jasmine-jstd-adapter git://github.com/ibolmo/jasmine-jstd-adapter.git
    jasmine-flight git://github.com/flightjs/jasmine-flight.git
    jasmine-signals git://github.com/AdamNowotny/jasmine-signals.git
    jasmine-underscore git://github.com/adscott/jasmine-underscore.git
    jasmine-bootstrap git://github.com/esbie/jasmine-bootstrap.git
    jasmine-data-provider git://github.com/sublimino/jasmine-data-provider.git
    jasmine-sugar git://github.com/fantactuka/jasmine-sugar.git

#安装jasmine类库
~ D:\workspace\javascript\jasmine>bower install jasmine
bower jasmine#*             not-cached git://github.com/pivotal/jasmine.git#*
bower jasmine#*                resolve git://github.com/pivotal/jasmine.git#*
bower jasmine#*               download https://github.com/pivotal/jasmine/archive/v1.3.1.tar.gz
bower jasmine#*                extract archive.tar.gz
bower jasmine#*               resolved git://github.com/pivotal/jasmine.git#1.3.1
bower jasmine#~1.3.1           install jasmine#1.3.1

jasmine#1.3.1 bower_components\jasmine
```

## 3. Jasmine环境配置

jasmine运行需要四个部分：

* 运行时环境：这里基于Chrome浏览器，通过HTML作为JS载体
* 源文件：用于实现某种逻辑的文件，就是平时写得JS脚本
* 测试文件：符合JasmineAPI的测试JS脚本
* 输出结果：jasmine提供了基于网页的输出结果



