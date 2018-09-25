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

