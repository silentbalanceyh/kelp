# KM10018 - Sass基础

css不能算是一种真正的编程语言，它本身无法完成像其他编程语言一样的嵌套、继承、设置变量等工作，为了解决这样的情况，开发者使用了sass这种“中间语言”，可以实现一些“编程”语言才有的功能，然后自动编译成css供浏览识别，这样既弥补了css的不足，也无需使用一种新的语言来代替css。

## 关于sass

sass是最早的css预处理语言，比less功能更强大，但因最开始的缩进语法不能被开发者接收，所以使用率病不搞，不过因为功能强加上RoR的推动，逐渐被接收。sass本身是使用ruby编写的css预处理语言，诞生于2007年，是最早成熟css预处理语言，最初是为了配合haml而设计，因此有和haml一样的缩进式风格。从sass第三代开始，放弃了缩进风格，并且完全向下兼容普通css代码，所以这一代的sass又称为scss。

## 使用sass

### 1.使用变量

sass让人受益的一个重要特性就是它为css引入了变量，你可以把反复使用的css属性值定义成变量，然后通过变量名来引用它们，而无需重复书写这一属性值。或者，对于仅使用过一次的属性值，你可以赋予其一个易懂的变量名，让人一眼就知道这个属性值的用途。sass使用`$`符号来标识变量（老版本的sass使用`!`来标识，改成`$`的原因多半是因为`!highlight-color`看起来很别扭。），相反习惯于`$highlight-color`和`$sidebar-width`。选择$也应该是它在CSS中没有其他用途，不会导致与现存或未来的css语法冲突。

#### 1.1. 变量声明

sass变量的声明和css属性的声明很像：

```sass
$highlight-color: #F90;
```

这个意味着变量`$highlight-color`现在的值是`#F90`，任何可以用作css属性值的赋值都可以用作sass的变量值，甚至是以空格分割的多个属性值，如`$basic-border: 1px solid black`，或以都好分割的多个属性值。

```sass
$plain-font: "Myriad Pro"、Myriad、"Helvetica Neue"、Helvetica、"Liberation Sans"、Arial和sans-serif; sans-serif;
```

这时变量还没有生效，除非你引用了这个变量——我们很快就会了解如何引用。与css属性不同，变量可以在css规则块定义之外存在。当变量定义在css规则块内，那么该变量只能在次规则块内使用；如果它们出现在任何形式的`{...}`块中（如`@media`或者`@font-face`块），情况也是如此：

```sass
$nav-color: #F90;
nav {
  $width: 100px;
  width: $width;
  color: $nav-color;
}

//编译后

nav {
  width: 100px;
  color: #F90;
}
```

在这段代码中，`$nav-color`这个变量定义在了规则块外边，所以在这个样式表中都可以像`nav`规则块那样引用它。`$width`这个变量定义在了nav的`{ }`规则块内，所以它只能在nav规则块内使用。这意味着是你可以在样式表的其他地方定义和使用`$width`变量，不会对这里造成影响。只声明变量其实没啥用处，我们最终的目的还是使用它们。上例已介绍了如何使用 `$nav-color`和`$width`这两个变量，接下来我们将进一步探讨变量的使用方法。

### 1.2.变量引用

凡是`css`属性的标准值（比如说1px或者bold）可存在的地方，变量就可以使用。`css`生成时，变量会被它们的值所替代。之后，如果你需要一个不同的值，只需要改变这个变量的值，则所有引用此变量的地方生成的值都会随之改变。

```sass
$highlight-color: #F90;
.selected {
  border: 1px solid $highlight-color;
}

//编译后

.selected {
  border: 1px solid #F90;
}
```

看上边示例中的`$highlight-color`变量，它被直接赋值给`border`属性，当这段代码被编译输出`css`时，`$highlight-color`会被`#F90`这一颜色值所替代。产生的效果就是给`selected`这个类一条1像素宽、实心且颜色值为`#F90`的边框。

在声明变量时，变量值也可以引用其他变量。当你通过粒度区分，为不同的值取不同名字时，这相当有用。下例在独立的颜色值粒度上定义了一个变量，且在另一个更复杂的边框值粒度上也定义了一个变量：

```sass
$highlight-color: #F90;
$highlight-border: 1px solid $highlight-color;
.selected {
  border: $highlight-border;
}

//编译后

.selected {
  border: 1px solid #F90;
}
```

这里，`$highlight-border`变量的声明中使用了`$highlight-color`这个变量。产生的效 果就跟你直接为`border`属性设置了一个`1px $highlight-color solid`的值是一样的。 最后，我们来了解一下变量命名的实用技巧，以结束关于变量的介绍。

### 1.3. 变量名中中划线还是下划线

`sass`的变量名可以与`css`中的属性名和选择器名称相同，包括中划线和下划线。这完全取决于个人的喜好，有些人喜欢使用中划线来分隔变量中的多个词（如`$highlight-color`），而有些人喜欢使用下划线（如`$highlight_color`）。使用中划线的方式更为普遍，这也是`compass`和本文都用的方式。

不过，`sass`并不想强迫任何人一定使用中划线或下划线，所以这两种用法相互兼容。用中划线声明的变量可以使用下划线的方式引用，反之亦然。这意味着即使`compass`选择用中划线的命名方式，这并不影响你在使用`compass`的样式中用下划线的命名方式进行引用：

```sass
$link-color: blue;
a {
  color: $link_color;
}

//编译后

a {
  color: blue;
}
```



