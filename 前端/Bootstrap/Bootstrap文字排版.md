#### 常用排版类

| 类 | 描述 |
| ---- | ---- |
|  .lead    | 突出显示，引导主体副本 |
| .small | 小文本 |
| .text-left/right/center | 文本左/右/居中对齐 |
| .text-justify/nowrap | 超出部分自动换行/不换行 |
| .text-lowercase/uppercase | 文本小写/大写 |
| .text-capitalize | 文本首字母大写 |
| .initialism | `<abbr>`元素文本小字体显示，并将小写字母转换为大写字母 |
| .blockquote-reverse | 应用右对齐 |
| .list-unstyled | 处理默认列表样式(`<ul>`和`<ol>`)，仅用于直接子列表 |
| .list-inline | 所有列表项放置在同一行 |
| .dl-horizontal | 浮动和偏移（`<dl>`和`<dt>`） |
| .pre-scollable | `<pre>`元素可滚动，代码块区域最大高度340px，超出高度出现滚动条 |

#### 一、全局文本样式

- `font-size`：14px；
- `line-height`：1.42857143（约20px）；
- `color`：#333（深灰色）；

#### 二、标题

##### （1）主标题 - h1~h6

- 用于标签和类

```html
<!-- 效果一样 -->
<h1>标题1</h1>
<p class="h1">标题2</p>
```



##### （2）内联子标题 - small

- 用于标签和类

```html
<!-- 效果一样 -->
<h1>标题<small>副标题</small></h1>
<h1>标题<span class="small">副标题</span></h1>
```

##### （3）引导主体副本 - lead

- 用于类；
- 给段落添加强调文本；

```html
<h2>标题</h2>
<p class="lead">这是标题副本</p>
```

