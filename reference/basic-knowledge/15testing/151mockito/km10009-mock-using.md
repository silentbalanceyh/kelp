# KM10009 - Mockito的使用

## 模拟对象

```java
// 模拟LinkedList的一个对象
LinkedList mockedList = mock(LinkedList.class);
// 此时调用get方法，会返回null，因为还没有对“方法调用”的返回值做模拟
System.out.println(mockedList.get(0));
```

## 模拟方法调用的返回值

```
// 模拟获取第一个元素时，返回字符串first，给特定的方法调用返回固定值在官方说法中称为stub。
when(mockedList.get(0)).thenReturn("first");
// 此时打印输出first
System.out.println(mockedList.get(0));
```



