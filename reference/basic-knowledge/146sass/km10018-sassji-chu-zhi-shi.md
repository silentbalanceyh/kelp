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

在上例中，`$link-color`和`$link_color`其实指向的是同一个变量。实际上，在`sass`的大 多数地方，中划线命名的内容和下划线命名的内容是互通的，除了变量，也包括对混合器和Sass函数的命名。但是在`sass`中纯`css`部分不互通，比如类名、ID或属性名。

尽管变量自身提供了很多有用的地方，但是`sass`基于变量提供的更为强大的工具才是我们关注的焦点。只有当变量与`sass`的其他特性一起使用时，才能发挥其全部的潜能。接下来，我们将探讨其中一个非常重要的特性，即规则嵌套。

## 2. 嵌套CSS规则

`css`中重复写选择器是非常恼人的。如果要写一大串指向页面中同一块的样式时，往往需要 一遍又一遍地写同一个`ID`

```sass
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

像这种情况，`sass`可以让你只写一遍，且使样式可读性更高。在Sass中，你可以像俄罗斯套娃那样在规则块中嵌套规则块。`sass`在输出`css`时会帮你把这些嵌套规则处理好，避免你的重复书写。

```sass
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}
```

```css
/* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

上边的例子，会在输出`css`时把它转换成跟你之前看到的一样的效果。这个过程中，`sass`用了两步，每一步都是像打开俄罗斯套娃那样把里边的嵌套规则块一个个打开。首先，把`#content`（父级）这个`id`放到`article`选择器（子级）和`aside`选择器（子级）的前边：

```sass
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  #content aside { background-color: #EEE }
}
```

```css
/* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
```

然后，`#content article`里边还有嵌套的规则，`sass`重复一遍上边的步骤，把新的选择器添加到内嵌的选择器前边。一个给定的规则块，既可以像普通的CSS那样包含属性，又可以嵌套其他规则块。当你同时要为一个容器元素及其子元素编写特定样式时，这种能力就非常有用了。

```sass
#content {
  background-color: #f5f5f5;
  aside { background-color: #eee }
}
```

容器元素的样式规则会被单独抽离出来，而嵌套元素的样式规则会像容器元素没有包含任何属性时那样被抽离出来。

```css
#content { background-color: #f5f5f5 }
#content aside { background-color: #eee }
```

大多数情况下这种简单的嵌套都没问题，但是有些场景下不行，比如你想要在嵌套的选择器 里边立刻应用一个类似于`：hover`的伪类。为了解决这种以及其他情况，`sass`提供了一个特殊结构`&`。

### 2.1.父选择器的标示符&

一般情况下，`sass`在解开一个嵌套规则时就会把父选择器（`#content`）通过一个空格连接到子选择器的前边（`article`和`aside`）形成（`#content article`和`#content aside`）。这种在CSS里边被称为后代选择器，因为它选择ID为`content`的元素内所有命中选择器`article`和`aside`的元素。但在有些情况下你却不会希望`sass`使用这种后代选择器的方式生成这种连接。

最常见的一种情况是当你为链接之类的元素写`：hover`这种伪类时，你并不希望以后代选择器的方式连接。比如说，下面这种情况`sass`就无法正常工作：

```sass
article a {
  color: blue;
  :hover { color: red }
}
```

这意味着`color: red`这条规则将会被应用到选择器`article a :hover`，`article`元素内链接的所有子元素在被`hover`时都会变成红色。这是不正确的！你想把这条规则应用到超链接自身，而后代选择器的方式无法帮你实现。

解决之道为使用一个特殊的`sass`选择器，即父选择器。在使用嵌套规则时，父选择器能对于嵌套规则如何解开提供更好的控制。它就是一个简单的`&`符号，且可以放在任何一个选择器可出现的地方，比如`h1`放在哪，它就可以放在哪。

```sass
article a {
  color: blue;
  &:hover { color: red }
}
```

当包含父选择器标识符的嵌套规则被打开时，它不会像后代选择器那样进行拼接，而是`&`被父选择器直接替换：

```sass
article a { color: blue }
article a:hover { color: red }
```

在为父级选择器添加`：hover`等伪类时，这种方式非常有用。同时父选择器标识符还有另外一种用法，你可以在父选择器之前添加选择器。举例来说，当用户在使用IE浏览器时，你会通过`JavaScript`在`<body>`标签上添加一个ie的类名，为这种情况编写特殊的样式如下：

```sass
#content aside {
  color: red;
  body.ie & { color: green }
}
```

```css
/*编译后*/
#content aside {color: red};
body.ie #content aside { color: green }
```

`sass`在选择器嵌套上是非常智能的，即使是带有父选择器的情况。当`sass`遇到群组选择器（由多个逗号分隔开的选择器形成）也能完美地处理这种嵌套。

### 2.2.群组选择器的嵌套

在`CSS`里边，选择器```h1``h2```和`h3`会同时命中h1元素、h2元素和h3元素。与此类似，`.button button`会命中button元素和类名为.button的元素。这种选择器称为群组选择器。群组选择器 的规则会对命中群组中任何一个选择器的元素生效。

```sass
.button, button {
  margin: 0;
}
```

当看到上边这段代码时，你可能还没意识到会有重复性的工作。但会很快发现：如果你需要在一个特定的容器元素内对这样一个群组选择器进行修饰，情况就不同了。`css`的写法会让你在群组选择器中的每一个选择器前都重复一遍容器元素的选择器。

```sass
.container h1, .container h2, .container h3 { margin-bottom: .8em }
```

非常幸运，`sass`的嵌套特性在这种场景下也非常有用。当`sass`解开一个群组选择器规则内嵌的规则时，它会把每一个内嵌选择器的规则都正确地解出来：

```sass
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
```

首先`sass`将`.container`和```h1``.container```和```h2``.container```和`h3`分别组合，然后将三 者重新组合成一个群组选择器，生成你前边看到的普通`css`样式。对于内嵌在群组选择器内的嵌 套规则，处理方式也一样：

```sass
nav, aside {
  a {color: blue}
}
```

首先`sass`将`nav`和```a``aside```和`a`分别组合，然后将二者重新组合成一个群组选择器：

```sass
nav a, aside a {color: blue}
```

处理这种群组选择器规则嵌套上的强大能力，正是`sass`在减少重复敲写方面的贡献之一。尤其在当嵌套级别达到两层甚至三层以上时，与普通的`css`编写方式相比，只写一遍群组选择器大大减少了工作量。有利必有弊，你需要特别注意群组选择器的规则嵌套生成的`css`。虽然`sass`让你的样式表看上去很小，但实际生成的`css`却可能非常大，这会降低网站的速度。关于选择器嵌套的最后一个方面，我们看看`sass`如何处理组合选择器，比如&gt;、+和~的使用。你将看到，这种场景下你甚至无需使用父选择器标识符。

### 2.3. 子组合选择器和同层组合选择器：&gt;, +, ~

上边这三个组合选择器必须和其他选择器配合使用，以指定浏览器仅选择某种特定上下文中的元素。

```sass
article section { margin: 5px }
article > section { border: 1px solid #ccc }
```

你可以用子组合选择器&gt;选择一个元素的直接子元素。上例中，第一个选择器会选择article下的所有命中section选择器的元素。第二个选择器只会选择article下紧跟着的子元素中命中section选择器的元素。

在下例中，你可以用同层相邻组合选择器`+`选择`header`元素后紧跟的`p`元素：

```sass
header + p { font-size: 1.1em }
```

你也可以用同层全体组合选择器`~`，选择所有跟在`article`后的同层`article`元素，不管它们之间隔了多少其他元素：

```sass
article ~ article { border-top: 1px dashed #ccc }
```

这些组合选择器可以毫不费力地应用到`sass`的规则嵌套中。可以把它们放在外层选择器后边，或里层选择器前边：

```sass
article {
  ~ article { border-top: 1px dashed #ccc }
  > section { background: #eee }
  dl > {
    dt { color: #333 }
    dd { color: #555 }
  }
  nav + & { margin-top: 0 }
}
```

`sass`会如你所愿地将这些嵌套规则一一解开组合在一起：

```css
article ~ article { border-top: 1px dashed #ccc }
article > footer { background: #eee }
article dl > dt { color: #333 }
article dl > dd { color: #555 }
nav + article { margin-top: 0 }
```

在`sass`中，不仅仅`css`规则可以嵌套，对属性进行嵌套也可以减少很多重复性的工作。

### 2.4. 嵌套属性

在`sass`中，除了CSS选择器，属性也可以进行嵌套。尽管编写属性涉及的重复不像编写选择器那么糟糕，但是要反复写```border-style``border-width``border-color```以及`border-*`等也是非常烦人的。在`sass`中，你只需敲写一遍`border`

```sass
nav {
  border: {
  style: solid;
  width: 1px;
  color: #ccc;
  }
}
```

嵌套属性的规则是这样的：把属性名从中划线-的地方断开，在根属性后边添加一个冒号:，紧跟一个`{ }`块，把子属性部分写在这个`{ }`块中。就像`css`选择器嵌套一样，`sass`会把你的子属性一一解开，把根属性和子属性部分通过中划线-连接起来，最后生成的效果与你手动一遍遍写的`css`样式一样：

```sass
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}
```

对于属性的缩写形式，你甚至可以像下边这样来嵌套，指明例外规则：

```sass
nav {
  border: 1px solid #ccc {
  left: 0px;
  right: 0px;
  }
}
```

这比下边这种同等样式的写法要好：

```sass
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
```

属性和选择器嵌套是非常伟大的特性，因为它们不仅大大减少了你的编写量，而且通过视觉上的缩进使你编写的样式结构更加清晰，更易于阅读和开发。

即便如此，随着你的样式表变得越来越大，这种写法也很难保持结构清晰。有时，处理这种大量样式的唯一方法就是把它们分拆到多个文件中。`sass`通过对`css`原有`@import`规则的改进直接支持了这一特性。

## 3. 导入SASS文件：

`css`有一个特别不常用的特性，即`@import`规则，它允许在一个`css`文件中导入其他`css`文件。然而，后果是只有执行到`@import`时，浏览器才会去下载其他`css`文件，这导致页面加载起来特别慢。

`sass`也有一个`@import`规则，但不同的是，`sass`的`@import`规则在生成`css`文件时就把相关文件导入进来。这意味着所有相关的样式被归纳到了同一个`css`文件中，而无需发起额外的下载请求。另外，所有在被导入文件中定义的变量和混合器（参见2.5节）均可在导入文件中使用。

使用`sass`的`@import`规则并不需要指明被导入文件的全名。你可以省略`.sass`或`.scss`文件后缀（见下图）。这样，在不修改样式表的前提下，你完全可以随意修改你或别人写的被导入的`sass`样式文件语法，在`sass`和`scss`语法之间随意切换。举例来说，`@import`"sidebar";这条命令将把`sidebar.scss`文件中所有样式添加到当前样式表中。

![](/assets/10018/001.png)

本节将介绍如何使用`sass`的`@import`来处理多个`sass`文件。首先，我们将学习编写那些被导入的`sass`文件，因为在一个大型`sass`项目中，这样的文件是你最常编写的那一类。接着，了解集中导入`sass`文件的方法，使你的样式可重用性更高，包括声明可自定义的变量值，以及在某一个选择器范围内导入`sass`文件。最后，介绍如何在`sass`中使用`css`原生的`@import`命令。

通常，有些`sass`文件用于导入，你并不希望为每个这样的文件单独地生成一个`css`文件。对此，`sass`用一个特殊的约定来解决。

### 3.1.使用SASS部分文件

当通过`@import`把`sass`样式分散到多个文件时，你通常只想生成少数几个`css`文件。那些专门为`@import`命令而编写的`sass`文件，并不需要生成对应的独立`css`文件，这样的`sass`文件称为局部文件。对此，`sass`有一个特殊的约定来命名这些文件。

此约定即，`sass`局部文件的文件名以下划线开头。这样，`sass`就不会在编译时单独编译这个文件输出`css`，而只把这个文件用作导入。当你`@import`一个局部文件时，还可以不写文件的全名，即省略文件名开头的下划线。举例来说，你想导入`themes/_night-sky.scss`这个局部文件里的变量，你只需在样式表中写```@import``"themes/night-sky";```。

局部文件可以被多个不同的文件引用。当一些样式需要在多个页面甚至多个项目中使用时，这非常有用。在这种情况下，有时需要在你的样式表中对导入的样式稍作修改，`sass`有一个功能刚好可以解决这个问题，即默认变量值。

### 3.2.默认变量值

一般情况下，你反复声明一个变量，只有最后一处声明有效且它会覆盖前边的值。举例说明：

```sass
$link-color: blue;
$link-color: red;
a {
color: $link-color;
}
```

在上边的例子中，超链接的`color`会被设置为`red`。这可能并不是你想要的结果，假如你写了一个可被他人通过`@import`导入的`sass`库文件，你可能希望导入者可以定制修改`sass`库文件中的某些值。使用`sass`的`!default`标签可以实现这个目的。它很像`css`属性中`!important`标签的对立面，不同的是`!default`用于变量，含义是：如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

```sass
$fancybox-width: 400px !default;
.fancybox {
width: $fancybox-width;
}
```

在上例中，如果用户在导入你的`sass`局部文件之前声明了一个`$fancybox-width`变量，那么你的局部文件中对`$fancybox-width`赋值`400px`的操作就无效。如果用户没有做这样的声明，则`$fancybox-width`将默认为`400px`。

接下来我们将学习嵌套导入，它允许只在某一个选择器的范围内导入`sass`局部文件。

### 3.3.嵌套导入

跟原生的`css`不同，`sass`允许`@import`命令写在`css`规则内。这种导入方式下，生成对应的`css`文件时，局部文件会被直接插入到`css`规则内导入它的地方。举例说明，有一个名为`_blue-theme.scss`的局部文件，内容如下：

```sass
aside
 {

background
: blue;

color
: white;
}
```

然后把它导入到一个CSS规则内，如下所示：

```sass
.blue-theme {@import"blue-theme"}


//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: 
#fff;

  }
}
```

被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。这些变量和混合器不会全局有效，这样我们就可以通过嵌套导入只对站点中某一特定区域运用某种颜色主题或其他通过变量配置的样式。

有时，可用`css`原生的`@import`机制，在浏览器中下载必需的`css`文件。`sass`也提供了几种方法来达成这种需求。

### 3.4.原生的CSS导入：

由于`sass`兼容原生的`css`，所以它也支持原生的`CSS@import`。尽管通常在`sass`中使用`@import`时，`sass`会尝试找到对应的`sass`文件并导入进来，但在下列三种情况下会生成原生的`CSS@import`，尽管这会造成浏览器解析`css`时的额外下载：

* 被导入文件的名字以`.css`结尾；
* 被导入文件的名字是一个URL地址（比如[http://www.sass.hk/css/css.css](http://www.sass.hk/css/css.css)），由此可用谷歌字体API提供的相应服务；
* 被导入文件的名字是`CSS`的url\(\)值。

这就是说，你不能用`sass`的`@import`直接导入一个原始的`css`文件，因为`sass`会认为你想用`css`原生的`@import`。但是，因为`sass`的语法完全兼容`css`，所以你可以把原始的`css`文件改名为`.scss`后缀，即可直接导入了。

文件导入是保证`sass`的代码可维护性和可读性的重要一环。次之但亦非常重要的就是注释了。注释可以帮助样式作者记录写`sass`的过程中的想法。在原生的`css`中，注释对于其他人是直接可见的，但`sass`提供了一种方式可在生成的`css`文件中按需抹掉相应的注释。

