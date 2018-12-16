> 操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是属性，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将 `v-bind` 用于 `class` 和 `style` 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串之外，还可以是对象或数组。

## 绑定class

##### 传给`v-bind:class`一个对象，以动态切换class

```html
<div v-bind:class="{active:isActive}"></div>
```

- 这个`active`的`class`存在与否取决于`isActive`是`true` or `false`；