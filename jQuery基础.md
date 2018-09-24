### 加载方式

##### 1、本地加载

​	类似JS文件的加载方式

##### 2、网络加载

可以选择从其他站点服务器加载，好处是，一般知名网站大多数人都访问过，本地都会有下载文件，提高加载速度:

百度CDN：

```html
<head>
    <script src="https://apps.bdimg.com/libs/jquery/2.1.4/jquery.min.js"></script>
</head>
```

BootCDN：

```html
<head>
	<script src="https://cdn.bootcss.com/jquery/1.10.2/jquery.min.js"></script>
</head>
```

### 语法

##### $(selector).action()

示例：

```js
$(this).hide()	//隐藏当前元素
$("p").hide()	//隐藏所有<p>元素
```

### 文档就绪事件

```javascript
$(document).ready(function(){
    //js代码
});

$(function(){
    //js代码
});
```

- 问了防止文档在完全假造之前执行jQuery代码
- 上面两种方法效果相同

### 选择器

- 以`$("")`开头；
- 内部元素选取方式和JavaScript相同；

### 事件

- jQuery是为事件处理特别设计的；
- 事件即响应；

##### 常用的jQuery事件

|           代码            |       说明       |
| :-----------------------: | :--------------: |
| ```$(document).ready()``` |   文档加载完成   |
|        `.click()`         |  selector被点击  |
|       `.dblclick()`       |  selector被双击  |
|      `.mouseenter()`      | 鼠标进入selector |
|      `.mouseleave()`      |     鼠标离开     |
|      `.mousedown()`       |     鼠标按下     |
|       `.mouseup()`        |     鼠标松开     |
|        `.hover()`         |     鼠标悬停     |
|        `.focus()`         | selector获取焦点 |
| `.blur()` | selector失去焦点 |
| `.keypress()` | 键盘按键按下 |
| `.keyup()` | 键盘按键弹起 |
| `.keypress()` | 键盘字符按键（不包括功能按键）按下 |



