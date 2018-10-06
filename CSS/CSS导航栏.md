### 一、将列表做成导航栏

- 使用`list-style-type:none`属性去掉前面的列表标记；
- 设置边距和填充为0；

示例：

```css
ul{
    list-style-type:none;
    margin:0;
    padding:0;
}
```

### 二、垂直导航栏

示例：

```html
<ul>
	<li><a class="active" href="#home"></a>主页</li>
    <li><a href="#news">新闻</a></li>
    <li><a href="#contact">联系</a></li>
    <li><a href="#about">关于</a></li>
</ul>
```

```css
ul{
    list-style-type: none;	//去掉列表标记
    margin: 0;
    padding: 0;
    width: 200px;
    background-color: red;
}
li a{
    display: block;
    color: blue;
    padding: 10px 20px;
    text-decoration: none;
}
li a.active{
    
}
/*非active类的其他li的悬停样式*/
li a:hover:not(.active){
    
}
```

### 三、水平导航栏

- 可以使用内联(`inline`)或浮动(`float`)的方式实现；
- 想要链接块具有相同的大小，必须使用浮动的方式；

##### （1） inline方式

- 默认情况下，`<li>`是块元素；
- `display:inline`会使得`width/height`失效，尺寸根据文字来定；

示例：

```css
li{
    display: inline;
}
```

##### （2）float方式

- 可以指定具体宽度；
- `display:block`：显示块元素链接，指定具体宽度；
- `float:left`：浮动元素块，使得彼此相邻；

示例：

```css
li{
    float: left;
}
a{
    display:block;
    width:100px;
}
```

### 四、实例

##### （1）水平导航条

```css
ul{
    list-style-type: none;
    margin: 0;
    padding: 0;
    overflow: hidden;
    background-color: blue;
}
li{
    float: left;
}
li a{
    display: block;
    color: white;
    text-align: center;
    padding: 14px 16px;
    text-decoration: none;
}
li a:hover{
    background-color:red;
}
```

##### （2）链接右对齐

```html
<ul>
    <li><a href="#home">主页</a></li>
    <li style="float:right"><a href="#about">关于</a></li>
</ul>
```

##### （3）固定导航条

```css
/*固定在顶部*/
ul{
    postion: fixed;
    top: 0;
    width: 100%;
}
/*固定在底部*/
ul{
    postion: fixed;
    bottom: 0;
    width: 100%;
}
```

