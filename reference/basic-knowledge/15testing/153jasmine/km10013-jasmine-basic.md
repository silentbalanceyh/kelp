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



