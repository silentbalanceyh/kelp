## Scala基础知识

Scala的默认变量名：res0，res1，...

Scala中的两种变量：val和var，val类似final变量、var就是普通变量，也就是说，val变量在设置值了过后不可更改（值不改，引用不发生变化）

Scala中的函数定义：

```scala
def max(x: Int, y: Int): Int = {
    if(x > y) x
    else y
}
```

\*：Scala支持类型推断，所以并不是所有的定义都需要写类型定义。



