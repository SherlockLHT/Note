### 一、获取/设置内容和属性

```javascript
$(selector).text()	//获取/设置文本内容
$(selector).html()	//获取/设置内容，包括html标记
$(selector).val()	//获取/设置表单字段的值
$(selector).attr()	//获取/设置指定属性
```

示例：

```javascript
$("p").attr("background-color")		//获取背景属性
$("p").attr("background-color", "#123456")	//设置背景属性
```

### 二、添加元素

```javascript
$(selector).append()
$(selector).prepend()
$(selector).after()
$(selector).before()
```

#### 注意点

- append()和after()在元素内插入内容，新增的内容变成该元素的子元素或节点
- after()和before()在元素外插入内容，新增的内容变成该元素的兄弟节点

示例：

原内容

```html
<div class="a">
    <div class="b"></div>
</div>
```
执行1：

```javascript
$(".a").append($(".c"));
```

结果：

```html
<div class="a">
    <div class="b"></div>
    <div class="c"></div>
</div>
```

执行2：

```javascript
$(".a").after($(".c"));
```

结果：

```html
<div class="a">
    <div class="b"></div>
</div>
<div class="c"></div>
```

### 三、删除元素

```javascript
$(selector).remove(condition)	//删除元素
$(selector).empty()			//清空元素内容，包含子元素
```

示例：

```javascript
$("p").remove()			//删除所有p元素
$("p").remove(".test")	//删除class=test的所有p元素
$("p").empty()			//清空所有p元素内容
```

### 四、操作CSS

```javascript
$(selector).addClass()		//添加一个/多个类
$(selector).removeClass()	//删除一个/多个类
$(selector).toggleClass()	//添加/删除一个/多个类，有则删除，没有则添加
$(selector).css()			//设置/返回样式属性
```

- 多个类中间用空格隔开

示例：

```javascript
$("p").addClass("red")			//添加red类
$("p").addClass("red test")		//添加red和test类
$("p").css("background-color")	//获取p元素的背景色，若有多个，则返回第一个
$("p").css("background-color", "red")	//设置所有的p元素的背景色为红色
$("P").css({"color":"red","font-size":"20"})	//设置所有p元素的多个属性
```

### 五、尺寸

```javascript
$(selector).width()			//设置/返回元素宽度，不包括内边距、边框和外边距
$(selector).height()
$(selector).innerWidth()	//设置/返回元素宽度，包括内边距
$(selector).innerHeight()
$(selector).outerWidth(isContainMargin)	//返回元素宽度，包括内边距和边框
$(selector).outerHeight(isContainMargin)
```

- isContainMargin决定是否包含外边距，值：true/false

