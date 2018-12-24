##### *Vue Router* 为*Vue*官方的路由库；

##### *Vue* + *Vue Router*可以创建单页面应用；

##### *Vue Router*用于将*component* 映射到*routes* 然后渲染；

## 示例：

```html
<div id="app">
    <h1>Hello APP!</h1>
    <p>
        <!-- 使用router-link组件来导航 -->
        <!-- 传入to属性指定连接 -->
        <!-- <router-link>默认会被渲染成一个<a>标签 -->
        <router-link to="/foo">Go to Foo</router-link>
        <router-link to="/bar">Go to Bar</router-link>
    </p>
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
</div>
```

```js
//定义路由组件，也可以从其他文件import进来
const Foo = {template: "<div>foo</div>"};
const Bar = {template: "<div>bar</div>"};
//定义路由
//每个路由应该映射一个组件
const routes = [
	{path: "/foo", component: Foo},
	{path: "/bar", component: Bar},
];
//创建router实例，传入定义的路由配置
const router = new VueRouter({
	routes: routes
});
//创建和挂在根实例
//通过router参数注入路由，以让整个应用都有路由功能
const app = new Vue({
	router
}).$mount("#app");
```

## 动态路由匹配

##### 给路由传参

```html
<div id="app">
    <h1>Hello APP!</h1>
    <p>
        <!-- 使用router-link组件来导航 -->
        <!-- 传入to属性指定连接 -->
        <!-- <router-link>默认会被渲染成一个<a>标签 -->
        <router-link to="/user/foo">Go to Foo</router-link>
        <router-link to="/user/bar">Go to Bar</router-link>
    </p>
    <!-- 路由出口 -->
    <!-- 路由匹配到的组件将渲染在这里 -->
    <router-view></router-view>
</div>
```

```js
const User = {
	//template: "<div>User</div>"
	template: "<div>{{ $route.params.id }}</div>",
}
//定义路由
//每个路由应该映射一个组件
const routes = [
	//动态路径参数，以冒号开头
	{path: "/user/:id", component: User}
];
//创建router实例，传入定义的路由配置
const router = new VueRouter({
	routes: routes
});
//创建和挂在根实例
//通过router参数注入路由，以让整个应用都有路由功能
const app = new Vue({
	router
}).$mount("#app");
```

