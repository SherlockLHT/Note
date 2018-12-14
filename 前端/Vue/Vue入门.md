## 声明式渲染

- 模板语法；
- 响应式；
- 绑定时，对该元素及其子元素均有效；

#### （1）绑定数据

**代码示例：**

```html
<div id="app">
	{{ message }}
</div>
<button>改变值</button>
```

```js
window.onload = function () {
	var app = new Vue({
		el: '#app',
		data: {
			message: 'Hello Vue!'
		}
	});
	document.querySelector("button").onclick=function(){
		app.message = '111';//更改div内的数据
	}
}
```

- 修改`app.message`的值，页面也会随之刷新；

#### （2）绑定属性

**代码示例：**

```html
<div id="app-2">
    <!-- 绑定title属性 -->
	<span v-bind:title="message">鼠标悬停</span>
</div>
<button>改变值</button>
```

```js
var app2 = new Vue({
	el:'#app-2',
	data:{
		message:'页面加载于' + new Date().toLocaleString()
	}
});
document.querySelector("button").onclick=function(){
	app2.message = "changed";//改变title属性内容
}
```

- `v-bind`特性称为**指令**，以`v-`开头，以表示是`vue`提供的特殊特性，包括下例的`v-if`等；
- `v-bind:title`表示绑定该元素的`title`属性；

