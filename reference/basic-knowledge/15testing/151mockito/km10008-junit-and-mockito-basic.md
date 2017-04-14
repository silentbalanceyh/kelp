# KM10008 - Mockito测试基础

Mock测试是单元测试的重要方法之一，本文主要介绍基于Java语言的Mock测试框架 -- Mockito的使用。

## 什么是Mock？

Mock测试就是在测试过程中，对于一些不容易构造的（如HttpServletRequest必须在Servlet容器中才能构造）或不容易获取比较复杂的对象（如JDBC中的ResultSet对象），用一个虚拟对象（Mock对象）来创建方便测试的测试方法。

Mac最大的功能是帮你把单元测试的耦合分解开，如果您的代码对另一个类或者接口有依赖，它能够帮你模拟这些依赖，并帮您验证所调用的依赖行为。

比如代码中有这样的依赖：

![](/_images/km/testing/KM10008-001.jpg)

当我们需要测试A类的时候，如果没有Mock，则我们需要把整个依赖树都构建出来，而使用Mock的话就可以将结构分解开，像下面这样：

![](/_images/km/testing/KM10008-002.jpg)

## Mock测试介绍

### 使用范畴

真实对象具有不可确定的行为，产生不可预测的结果，（如：股票行情，天气预报）真实对象很难被创建的，真实对象的某些行为很难被触发，真实对象实际上还不存在的（和其他开发小组或者新硬件打交道）等等。

### 测试关键步骤

使用一个接口来描述这个对象，在产品代码中实现这个接口，在测试代码中实现这个接口，在北侧代码中只是通过接口来引用对象，所以它不知道这个接口中的实现对象是真实对象还是Mock对象。

### Java Mock测试

目前在Java中主要Mock测试工具包括：`Mockito` ，`JMock`，`MockCreator` ，`Mockrunner`，`EasyMock`，`MockMaker`等，这些框架的比较不是重点，本文主要介绍Mockito的入门。

## Mockito的特性

Mockito是一个很美味的单元测试Mock框架。

大多Java Mock库如EasyMock或JMock都是`expect-run-verify`（期望-运行-验证）方式，而Mockito使用更简单、更直观：在执行后的互动中提问。使用Mockito，您可以验证任何您想要的，而那些使用`expect-run-verify`方式的库，您尝尝被迫查看无关的交互。非`expect-run-verify`方式也意味着，Mockito无需准备昂贵的前期启动，他们的目标是透明的，让开发人员专注于测试选定的行为。

Mockito拥有很少的API，所有开始使用Mockito，几乎没有时间成本，因为只有一种创造Mock的方式。只要记住，在执行前sbub，而在交互中验证，您很快就会发现这样的TDD java代码很自然。

类似EasyMock的语法来的，所以您可以放心地重构，Mockito并不需要"expectation"（期望）的概念，只有stub和验证。Mockito实现了[Gerard Meszaros](http://xunitpatterns.com/Mocks, Fakes, Stubs and Dummies.html)所谓的Test Spy.

### 其他特点：

* 可以mock具体类而不单只是接口
* 一点注解语法糖 - @Mock
* 干净的验证错误是 - 点击堆栈跟踪，看看在测试中的失败验证；点击一场的原因来导航到代码中的实际互动，堆栈跟踪总是干干净净。
* 允许灵活有序的验证（如：您任意有序`verify`，而不是一个单独的交互）
* 支持”详细的用户号码的时间“以及”至少一次“验证
* 灵活的验证或使用参数匹配器的stub（`anyObject()`，`anyString()`或`refEq()`用于基于反射的相等匹配）
* 允许创建自定义参数匹配器、或者使用现有的hamcrest匹配器

## Mock中基本概念

### 检测方法

首先需要介绍的是检测的方式，我们写单元测试，以及使用Mockito都是为了检验我们的代码是否按照我们预期那样运行。一般有两种检测方式：

* 状态检测（state verification）：方法运行之后，通过检测方法的状态（或者说返回值）进行判断方法是否运行成功。
* 行为检测（behavior verification）：方法运行之后，通过检测方法的执行行为（或者说执行顺序）运行判断方法是否运行成功。

### 替换对象

其次，这里介绍的是在mock对象时会使用到的不同替换对象，主要包含以下两种：

* 桩（stub）：模拟一个Object，当输入特定值的时候，返回Hard Code的指定值，并不真正执行逻辑，类似于复写（Override）了该方法，在复写方法中不执行任何逻辑，至返回特定值——多使用于状态检查。
* 模拟对象（mock）：模拟一个Object，相比于stub，mock更关心对象的期望行为，然后验证期望的行为是否发生，也就是说，在测试时，病不关心方法具体返回了什么，只关心某些特定的方法被使用到了，因此常用于行为验证。这种对于返回void的场景，有了很独特的优势。

## Mock的好处

### 提前创建测试，TDD

这是个最大的好处吧。如果你创建了一个Mock那么你就可以在service接口创建之前写Service Tests了，这样你就能在开发过程中把测试添加到你的自动化测试环境中了。换句话说，模拟使你能够使用测试驱动开发。

### 团队可并行工作

这类似于上面的那点；为不存在的代码创建测试。但前面讲的是开发人员编写测试程序，这里说的是测试团队来创建。当还没有任何东西要测的时候测试团队如何来创建测试呢？模拟并针对模拟测试！这意味着当service借口需要测试时，实际上QA团队已经有了一套完整的测试组件；没有出现一个团队等待另一个团队完成的情况。这使得模拟的效益型尤为突出了。

### 您可以创建一个验证或演示程序

由于Mocks非常高效，Mocks可以用来创建一个概念证明，作为一个示意图，或者作为一个你正考虑构建项目的演示程序。这为你决定项目接下来是否要进行提供了有力的基础，但最重要的还是提供了实际的设计决策。

### 为无法访问的资源编写测试

这个好处不属于实际效益的一种，而是作为一个必要时的“救生圈”。有没有遇到这样的情况？当你想要测试一个service接口，但service需要经过防火墙访问，防火墙不能为你打开或者你需要认证才能访问。遇到这样情况时，你可以在你能访问的地方使用MockService替代，这就是一个“救生圈”功能。

### Mock可以交给用户

在有些情况下，某种原因你需要允许一些外部来源访问你的测试系统，像合作伙伴或者客户。这些原因导致别人也可以访问你的敏感信息，而你或许只是想允许访问部分测试环境。在这种情况下，如何向合作伙伴或者客户提供一个测试系统来开发或者做测试呢？最简单的就是提供一个mock，无论是来自于你的网络或者客户的网络。soapUI mock非常容易配置，他可以运行在soapUI或者作为一个war包发布到你的java服务器里面。

### 隔离系统

有时，你希望在没有系统其他部分的影响下测试系统单独的一部分。由于其他系统部分会给测试数据造成干扰，影响根据数据收集得到的测试结论。使用mock你可以移除掉除了需要测试部分的系统依赖的模拟。当隔离这些mocks后，mocks就变得非常简单可靠，快速可预见。这为你提供了一个移除了随机行为，有重复模式并且可以监控特殊系统的测试环境。

## Spy

最后介绍一个Spy的东西，前边讲了Mock对象的两大功能，对于第二大功能：指定方法的特定行为——如果不指定怎么办？现在补充一下，若不指定的情况，一个mock对象的所有非void方法都将返回默认值：int、long类型方法将返回0，boolean方法将返回false，对象方法将返回null等，而void方法将什么都不做。

然后很多时候，您希望达到这样的效果：除非指定，否则调用这个对象的默认实现，同时又能拥有验证方法调用的功能。这正好是spy对象送实现的效果。创建一个spy对象以及spy对象d额用法介绍如下：

```java
//假设目标类的实现是这样的
public class PasswordValidator {
    public boolean verifyPassword(String password) {
        return "xiaochuang_is_handsome".equals(password);
    }
}

@Test
public void testSpy() {
    //跟创建mock类似，只不过调用的是spy方法，而不是mock方法。spy的用法
    PasswordValidator spyValidator = Mockito.spy(PasswordValidator.class);

    //在默认情况下，spy对象会调用这个类的真实逻辑，并返回相应的返回值，这可以对照上面的真实逻辑
    spyValidator.verifyPassword("xiaochuang_is_handsome"); //true
    spyValidator.verifyPassword("xiaochuang_is_not_handsome"); //false

    //spy对象的方法也可以指定特定的行为
    Mockito.when(spyValidator.verifyPassword(anyString())).thenReturn(true);

    //同样的，可以验证spy对象的方法调用情况
    spyValidator.verifyPassword("xiaochuang_is_handsome");
    Mockito.verify(spyValidator).verifyPassword("xiaochuang_is_handsome"); //pass
}
```

总之，spy和mock对象唯一的区别就是默认行为不一样：spy对象的方法默认调用真实的逻辑，mock对象的方法默认什么都不做，或直接返回默认值。

Mockito的使用参考：[KM10009 - Mockito Using](/reference/basic-knowledge/15testing/151mockito/km10009-mock-using.md)

