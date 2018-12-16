# 模板语法

- 绑定`DOM`到`Vue`实例；
- 响应式系统，`Vue`状态改变时，计算重新渲染组件的最小代价并应用到`OM`操作上；

## 一、插值

#### （1）文本

- 使用`{{}}`绑定`Vue`数据；

- 纯文本，不包含任何HTML语法，但是可以包含`js`语法（表达式）；

  **示例：**

  ```html
  <div id="app-10">
  	<p>{{10 + 12}}</p>	<!--22-->
  	<p>{{ok? "YES": "NO"}}</p>	<!--YES-->
  	<p>{{str + "?"}}</p>	<!--Test?-->
  </div>
  ```

  ```js
  var app1 = new Vue({
  	el: '#app-10',
  	data: {
  		ok: true,
  		str: "Test"
  	}
  });
  ```

#### （2）HTML

- 使用`v-html`指定，输出HTML代码；

- 与***文本***的区别在于可以包含HTML语法；

  **示例：**

  ```html
  <div id="app" v-html="message"></div>
  ```

  ```js
  var app = new Vue({
      el: '#app',
      data: {
          message: "<h1>HTML文本测试</h1>"
      }
  });
  ```

> 你的站点上动态渲染的任意 HTML 可能会非常危险，因为它很容易导致 [XSS 攻击](https://en.wikipedia.org/wiki/Cross-site_scripting)。请只对可信内容使用 HTML 插值，**绝不要**对用户提供的内容使用插值。

#### （3）特性

- 使用`b-bind`指定绑定属性；

- 当时候布尔型修饰内容/属性时，`v-bind`将会控制该内容/属性是否存在（`null/undefined/false`表示该内容/属性不会被包含在渲染出来的元素中）；

  **示例：**

  ```css
  .class-1{
      background-color: green;
  }
  .class-2{
      color: yelow;
  }
  ```

  ```html
  <div id="vue-test" v-bind:class="{'class-1': true, 'class-2': true}">
  	<p v-html="text_1"></p>
  </div>
  ```

  ```js
  var app = new Vue({
      el: '#vue-test'
  });
  ```

  - 示例中，绑定`class`属性（使用方法和常用方法有所区别）；
  - 通过控制代码中的`true/false`来控制是否包含对应方法；
  - `class-1`的值若是`false`，则该元素的`class`属性就不会包含类`class-1`（后文的*class和style绑定*中具体细说）；

#### （4）指令（`v-`）

- 用于和`DOM`绑定；

- `v-if`用于判断；

- `v-bind`绑定属性；

- `v-on`监听`DOM`事件；

- 一个指令能够接收一个参数和一个修饰符，修饰符表示指令以特殊方式绑定；

  **修饰符**

  ```html
  <form v-on:submit.prevent="onSubmit"></form>
  ```

  - `prevent`修饰符告诉`v-on`指令，对于触发的事件调用`event.preventDefault()`；

#### （5）用户输入（`v-model`）

- 在`input`/`select`/`text`/`checkbox`/`radio`等，根据表单的值更新绑定的`DOM`元素的值；

## 二、缩写

- `vue`指令使用`v-`开头，表示，这个是`vue`的特性；

- 若所有模板的***单页面应用程序***都是`vue`构建的时，`v-`前缀则可有可无；

  #### `v-bind`缩写

  ```html
  <a v-bind:href="url">测试</a>
  <a :href="url"></a>
  ```

  #### `v-on`缩写

  ```html
  <a v-on:click="clickFuntion"></a>
  <a @click="clickFunction"></a>
  ```
