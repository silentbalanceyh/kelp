# KM10009 - Mockito的使用

## 模拟对象

```java
// 模拟LinkedList的一个对象
LinkedList mockedList = mock(LinkedList.class);
// 此时调用get方法，会返回null，因为还没有对“方法调用”的返回值做模拟
System.out.println(mockedList.get(0));
```



