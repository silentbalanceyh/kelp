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



