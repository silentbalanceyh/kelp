# KTS10003 - Inline Component

这些组件都依赖特殊的基础结构，有一些是通过JavaScript的API开放出来的，而另外一些则是通过`data-ts`属性开放出来的，在整个过程中不需要任何方式初始化这些组件。

### Button

```html
<button data-ts="Button" class="ts-primary">
	<span>Primary</span>
</button>
<button data-ts="Button" class="ts-secondary">
	<span>Secondary</span>
</button>
<button data-ts="Button" class="ts-tertiary">
	<span>Tertiary</span>
</button>
```

```html
<button data-ts="Button" class="ts-primary ts-micro">
	<span>Primary</span>
</button>
<button data-ts="Button" class="ts-secondary ts-micro">
	<span>Secondary</span>
</button>
<button data-ts="Button" class="ts-tertiary ts-micro">
	<span>Tertiary</span>
</button>
```



