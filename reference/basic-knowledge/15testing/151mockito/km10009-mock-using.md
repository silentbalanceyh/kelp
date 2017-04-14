# KM10009 - Mockito的使用

\*：关于方法的模拟限制，不可被Mock的方法：**final/private/equals\(\)/hashCode\(\)**。

## 模拟对象

```java
// 模拟LinkedList的一个对象
LinkedList mockedList = mock(LinkedList.class);
// 此时调用get方法，会返回null，因为还没有对“方法调用”的返回值做模拟
System.out.println(mockedList.get(0));
```

## 模拟方法调用的返回值

```java
// 模拟获取第一个元素时，返回字符串first，给特定的方法调用返回固定值在官方说法中称为stub。
when(mockedList.get(0)).thenReturn("first");
// 此时打印输出first
System.out.println(mockedList.get(0));
```

## 模拟方法调用抛出异常

```java
// 模拟获取第二个元素时，抛出RuntimeException
when(mockedList.get(1)).thenThrow(new RuntimeException());
// 此时将会抛出RuntimeException
System.out.println(mockedList.get(1));
```

如果一个函数没有返回值类型，那么可以使用该方法模拟异常抛出

```java
doThrow(new RuntimeException("clear exception")).when(mockedList).clear();
mockedList.clear();
```

## 模拟调用方法时的参数匹配

```java
// anyInt()匹配任何int参数，这意味着参数为任意值，其返回值均是element
when(mockedList.get(anyInt())).thenReturn("element");
// 此时打印的是element
System.out.println(mockedList.get(999));
```

## 模拟方法调用次数

```java
// 调用add一次
mockedList.add("now");
// 下面两个写法验证效果一样，均验证add方法是否被调用了一次
verify(mockedList).add("once");
verify(mockedList, times(1)).add("once");
```

## 校验行为

```java
// 模拟创建
List mockedList = mock(List.class);
// 使用mock对象
mockedList.add("one");
mockedList.clear();
// 验证
verify(mockedList).add("one");
verify(mockedList).clear();
```

## 模拟方法调用（Stubbing）

```java
// 您可以模拟对象的创建，不仅仅是接口模式
LinkedList mockedList = mock(LinkedList.class)
// 桩（Stubbing）
when(mockedList.get(0)).thenReturn("first");
when(mockedList.get(1)).thenThrow(new RuntimeException());
// 下边会打印“first”
System.out.println(mockedList.get(0));
// 下边抛出运行时异常
System.out.println(mockedList.get(1));
// 下边打印“null"，因为get(999)没有被模拟
System.out.println(mockedList.get(999));
verify(mockedList).get(0);
```

## 参数匹配

```java
// 使用内置anyInt()构造桩（Stubbing）进行参数匹配
when(mockedList.get(anyInt())).thenReturn("element");
// 使用自定义的匹配桩（根据自定义实现来校验isValid()）
when(mockedList.contains(argThat(isValid()))).thenReturn("element");
// 下边将会打印"element"
System.out.println(mockedList.get(999));
// 您同样可以验证参数匹配器
verify(mockedList).get(anyInt());
// 参数匹配器也可以使用Java 8的Lambdas
verify(mockedList).add(someString -> someString.length() > 5);
```

## 校验方法调用次数

```java
// 使用mock
mockedList.add("once");

mockedList.add("twice");
mockedList.add("twice");

mockedList.add("three times");
mockedList.add("three times");
mockedList.add("three times");
// 下边两个验证工作是一样的，看默认是否调用了一次
verify(mockedList).add("once");
verify(mockedList, times(1)).add("once");
// 确切调用次数验证
verify(mockedList, times(2)).add("twice");
verify(mockedList, times(3)).add("three times);
// 这里调用了never表示未调用，效果同times(0)
verify(mockedList, atLeastOnce()).add("three times");
verify(mockedList, atLeast(2)).add("five times");
verify(mockedList, atMost(5)).add("three times");
```

## 模拟无返回方法抛出异常

```java
doThrow(new RuntimeException()).when(mockedList).clear();
// 下边抛出RuntimeException
mockedList.clear();
```

## 校验方法调用顺序

```java
// A. 单独Mock特殊调用顺序的一堆方法
List singleMock = mock(List.class);
//    使用单个Mock
singleMock.add("was added first");
singleMock.add("was added second");
//    创建一个inOrder验证这个Mock
InOrder inOrder = inOrder(singleMock);
//    下边的行为将会先调用"was added first"，其次调用"was added second"
inOrder.verify(singleMock).add("was added first");
inOrder.verify(singleMock).add("was added second");

// B. 多个Mock按照t额定顺序执行
List firstMock = mock(List.class);
List secondMock = mock(List.class);
//    使用Mock
firstMock.add("was called first");
secondMock.add("was called second");
//    创建inOrder对象给任何需要验证顺序的Mock
InOrder inOrder = inOrder(firstMock,secondMock);
//    下边的顺序验证firstMock先调用，其次secondMock
inOrder.verify(firstMock).add("was called first");
inOrder.verify(secondMock).add("was called second");
// A + B将来可直接合并到一起
```

## 校验方法是否从未调用

```java
// 使用Mock - 仅仅使用一个mockOne交互
mockOne.add("one");
// 验证ordinary
verify(mockOne).add("one");
// 验证方法从未在mock中调用过
verify(mockOne, never()).add("two");
// 验证其他Mock是否未交互过
verifyZeroInteractions(mockTwo, mockThree);
```

## 快速创建Mock对象

```java
public class ArticleManagerTest {
      @Mock private ArticleCalculator calculator;
      @Mock private ArticleDatabase database;
      @Mock private UserProvider userProvider;
      @Before
      public void before(){
          MockitoAnnotations.initMocks(this);
      }
}
```

## 自定义返回不同结果

```java
when(mock.someMethod("some arg"))
   .thenThrow(new RuntimeException())     // 第一次会抛出异常
   .thenReturn("foo");                    // 第二次会返回这个结果
// 第一次调用抛出异常
mock.someMethod("some arg"); 
// 第二次调用，打印"foo"
System.out.println(mock.someMethod("some arg")); 
// 最后一个桩设置，打印“foo"
System.out.println(mock.someMethod("some arg")); // 第n次(n> 2)，依旧以最后返回最后一个配置
```

## 对返回结果进行拦截

```java
when(mock.someMethod(anyString())).thenAnswer(new Answer() {
    Object answer(InvocationOnMock invocation) {
        Object[] args = invocation.getArguments();
        Object mock = invocation.getMock();
        return "called with arguments: " + args;
    }
});
// 下图打印："called with arguments: foo"
System.out.println(mock.someMethod("foo"));
```

## Mock函数操作

可通过`doThrow()，doAnswer()，doNothing()，doReturn()，doCallRealMethod()`自定义函数操作。

## 暗中调用真实对象

```java
List list = new LinkedList();
List spy = spy(list);
// 可选的，你可在一些方法外设置桩:
when(spy.size()).thenReturn(100);
// 使用spy调用真实方法
spy.add("one");
spy.add("two");
// 打印one - List的第一个元素
System.out.println(spy.get(0));
// size()方法被调用 - 100会被打印
System.out.println(spy.size());
// 之后您可以自己验证
verify(spy).add("one");
verify(spy).add("two");
```

## 改变默认返回值

```java
Foo mock = mock(Foo.class, Mockito.RETURNS_SMART_NULLS);
Foo mockTwo = mock(Foo.class, new YourOwnAnswer());
```

## 捕获函数的参数值

```java
ArgumentCaptor<Person> argument = ArgumentCaptor.forClass(Person.class);
verify(mock).doSomething(argument.capture());
assertEquals("John", argument.getValue().getName());
```

## 部分Mock

```java
// 您可以创建spy()方法的部分Mock
List list = spy(new LinkedList());
// 启用Mock的选择
Foo mock = mock(Foo.class);
// 保证真实实现是”安全“的
// 如果真实实现抛出异常或者依赖特定的对待状态，则您就麻烦了
when(mock.someMethod()).thenCallRealMethod();
```

## 重置Mock

```java
List mock = mock(List.class);
when(mock.size()).thenReturn(10);
mock.add(1);
reset(mock);
// Mock这时候忘记了interactions & stubbing
```

## 序列化

```java
List<Object> list = new ArrayList<Object>();
List<Object> spy = mock(ArrayList.class, withSettings()
                 .spiedInstance(list)
                 .defaultAnswer(CALLS_REAL_METHODS)
                 .serializable());
```

## 检查超时

```java
// 在给定时间内执行完someMethod()
verify(mock, timeout(100)).someMethod();
// 上边的匿名用法是：
verify(mock, timeout(100).times(1)).someMethod();
// someMethod给定时间内执行了两次
verify(mock, timeout(100).times(2)).someMethod();
// someMethod在给定时间内至少执行了两次
verify(mock, timeout(100).atLeast(2)).someMethod();
// 验证someMethod的调用
// 若有自己验证模型就很有用
verify(mock, new Timeout(100, yourOwnVerificationMode)).someMethod();
```

## Mock详情

```java
Mockito.mockingDetails(someObject).isMock();
Mockito.mockingDetails(someObject).isSpy();
```



