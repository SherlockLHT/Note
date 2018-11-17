参考文章：https://www.cnblogs.com/jiangzhengbin/p/5688278.html

## `<a>`标签

`<a>`标签的`href`属性一般用于页面跳转，其值可以时有效的文档或者网络`url`等，也可以时`js`代码；

## 调用`js`代码

### （1）`href=javascript:;`

- `javascript:`是伪协议，避免页面跳转；

### （2）具体调用

#### 方法1：

```html
<a href="javascript:js_function();"></a>
```

- 这是常见的方法，但是这种方法再传递`this`参数时容易出问题；
- `javascript:`伪协议作为`<a>`的`href`属性的时候会导致不必要的触发`window.onbeforeunload`事件；
- 再IE中会导致gif停止播放；

综上，W3C标准不推荐使用。

#### 方法2 - 推荐使用：

```html
<a href="javascript:void(0);" onclick="js_function()"></a>
```

- 这是很多网站的最常用的方法，也是最周全的方法；
- `void(0)`返回`undefined`，地址不会发生跳转；
- `onclick`负责执行`js`函数；
- 此方法不会将`js`函数暴露给浏览器的状态栏；

#### 方法3 - 推荐使用：

```html
<a href="javascript:;" onclick="js_function()"></a>
```

- 此方法类似法二，区别是只执行一条空的`js`代码；

#### 方法4：

```html
<a href="#" onclick="js_function()"></a>
<a href="#" onclick="js_function();return false;"></a>
```

- `href="#"`会使得页面到顶部；
-  执行了`js`函数后，返回`false`，避免页面跳转；

