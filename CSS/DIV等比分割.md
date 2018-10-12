实现：子元素对父级div进行等比例分割

```html
<div class="outer">
	<div class="inner"></div>
	<div class="inner"></div>
    <div class="inner"></div>
</div>
```



#### （1）法一：浮动布局 + 百分比

- 加上边框导致内部div超出

```css
.outer{
    overflow: hidden;
    width: 500px;
    height: 200px;
    background-color: yellow;
}
.inner{
    background-color: red;
    float: left;
    width: 33.3%;
    height: 200px;
}
```

#### （2）法二：行内元素 + 百分比

- 效果较差，超出范围会换行

```css
.outer{
    overflow: hidden;
    width: 500px;
    height: 200px;
    background-color: yellow;
}
.inner{
    background-color: red;
    width: 33.3%;
    height: 200px;
    display: inline-block;
    margin: 0;
}
```

#### （3）父元素(display: table) + 子元素(display: table-cell)

```css
.outer{
    display: table;
    width: 500px;
    height: 200px;
    background-color: yellow;
}
.inner{
    background-color: red;
    width: 33.3%;
    height: 200px;
    display: table-cell;
}
```

#### （4）css3的flex布局

```css
.outer{
    display: table;
    width: 500px;
    height: 200px;
    background-color: yellow;
    display: flex;
}
.inner{
    flex: 1;
    border: 1px solid red;
}
```

#### （5）使用bootstrap的栅格系统