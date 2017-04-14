# KM10009 - Mockito的使用

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
// 单独Mock特殊调用顺序的一堆方法

```



