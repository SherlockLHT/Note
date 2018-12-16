## `v-if`/`v-else-if`/`v-else`

#### 用于条件判断

使用`v-if = "true"`来控制是否插入元素；

```html
<div id="app">
    <p v-if="type === 'A'">A</p>
    <p v--else-if="type === 'B'">B</p>
    <p v-else="type === 'C'">C</p>
</div>
```

```js
var app = new Vue({
    el:'#app',
    data: {
        type: "A"	//通过控制type，来控制显示对应元素
    }
});
```

## `v-show`

- 通过之控制是否显示元素；

```html
<p id="app" v-show="enabled">测试</p>
```

```js
var app = new Vue({
    el: '#app',
    data: {
        enabled: true
    }
});
```

