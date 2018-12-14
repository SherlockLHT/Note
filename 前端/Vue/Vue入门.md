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

#### （2）绑定属性（`v-bind`）

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

#### （3）条件（`v-if`）

**代码示例：**

```html
<div id="app-3">
	<p id="app-3" v-if="seen">现在能看到</p>
</div>
<button>改变值</button>
```

```js
var app3 = new Vue({
	el:'#app-3',
	data:{
		seen:true
	}
});
//控制元素可见
document.querySelector("button").onclick=function(){
	app3.seen=!app3.seen;
}
```

#### （4）循环（`v-for`）

**代码示例：**

```html
<div id="app-4">
	<ol>
        <!--item in todos遍历todos-->
		<li v-for="item in todos">
			{{item.text}}
		</li>
	</ol>
</div>
<button>改变值</button>
```

```js
var app4 = new Vue({
	el: '#app-4',
	data: {
		todos: [
			{ text: '学习 JavaScript' },
			{ text: '学习 Vue' }
		]
	}
});
document.querySelector("button").onclick=function(){
	app4.todos.push({text: '新增item'});
}
```

- `v-for`可以绑定数组的数据来渲染一个项目列表；

#### （5）事件（`v-on`）

- `v-on`指定添加事件监听器；


- 相同`Vue`实例中的数据可以使用`this`访问；

**示例1改写**

```html
<div id="app-5">
	{{ message }}
    <button id="app-5"  v-on:click="clickButton">改变值</button>
</div>

```

```js
var app5 = new Vue({
	el:"#app-5",
	data: {
		message: "Hello World!"
	},
	methods:{
		clickButton: function(){
			this.message = "changed";//改变本实例的数据
		}
	}
});
```

#### （6）绑定表单（`v-model`）

**示例代码：**

```html
<div id="app-6">
	<p>{{message}}</p>
	<input v-model="message">
</div>
```

```js
var app6 = new Vue({
	el:'#app-6',
	data:{
		message:"Hello World"
	}
});
```

- `v-model`将表单内容和数据绑定在一起，表单输入改变，绑定的数据跟随改变；