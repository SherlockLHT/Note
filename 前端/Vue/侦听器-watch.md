**响应数据变化，当需要数据在变化时执行异步或者开销较大的操作时，侦听器最有用。**

简单示例：

```html
<div id="app">
	<input type="text" v-model="content">
</div>
```

```js
new Vue({
	el: '#app',
	data: {
		content: "",
	},
	watch: {
        //当数据content改变时，触发该监听器
		content: function(){
			console.log("content has changed");
		}
	}
});
```

