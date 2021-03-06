#### Bootstrap常用input元素

- 文本输入框
- 下拉选择框
- 单选按钮
- 复选按钮
- 文本域
- 按钮

#### 表单样式类

| 类    | 说明                                                      |
| ----- | --------------------------------------------------------- |
| .form-group | 获取最佳间距 |
| .form-horizontal | 用于`<form>`，水平表单 |
| .form-inline | 用于`<form>`，内联表单 |
| .control-label | 定制样式效果 |
| .form-control | 定制样式效果 |

#### 控件样式类

| 类        | 说明        |
| --------- | ----------- |
| .checkbox | 用于`<div>`，处理控件和`label`的样式 |
| .checkbox-inline | 用于`<label>`包含`<input>`，内联控件 |
| .radio | 用于`<div>`，处理控件和`label`的样式 |
| .radio-inline | 用于`<label>`包含`<input>`，内联控件 |
| .input-sm/lg | 适用于`<input>/<textarea><select>`<br>控制控件高度（更小/更高） |
| .has-warning/error/success | 用于`<div>`，提供验证效果<br>警告状态（黄色）/错误状态（红色）/错误状态（红色） |
| .has-feedback | 表单验证中添加icon |
| .glyphicon | 使用bootstrap的免费图标，设置统一样式 |
| .glyphicon-ok/warning/remove | 勾号/警告/叉图标 |
| .glyphicon-search/asterisk... | 各种图标... |
| .form-control-feedback | ==未知== |
| .help-block | 信息提示 |

#### 按钮样式类
| 类 | 说明 |
| --------- | ----------- |
| .btn | 基础按钮 |
| .btn-default | 默认样式，可以有几种：primary/success/info/warning/danger/link |
| .btn-lg/sm/xs | 定义按钮大小，大/小/超小 |
| .btn-block | 将按钮的内联属性变为块状属性 |

#### 图像样式类

| 类              | 说明（用于`<img>`标签） |
| --------------- | ---- |
| .img-responsive | 响应式图片，针对响应式设计 |
| .img-rounded | 圆角图片 |
| .img-circle | 圆形图片 |
| .img-thumbnail | 缩略图 |


==在`<form>`中添加`role="form"`，增强语义，一般用于自定义组件==

### 一、垂直/基本表单

##### 基本表单创建方法

1. 标签和控件放在`<div class="form-group"></div>`中，以获取最佳间距；
2. 所有文本元素（`<input>/<textarea>/<select>`添加`class="form-control"`）；

### 二、内联表单

- 一行，左对齐；
- 使用类`.sr-only`可以隐藏内联表单的标签，比如和`text`绑定的`label`；
- 默认情况下， Bootstrap中的`input`、`select`和`textarea`有100%的宽度，使用内联表单，需要设置宽度；

##### 创建方法

- `<form>`标签添加类`.form-inline`；

### 三、水平表单

##### 创建方法

1. `<form>`添加类`.form-horizontal`；
2. 标签和控件放在```<div class="form-group"></div>```中，以获取最佳间距；
3. 标签添加类`.control-label`；

### 四、表单控件

#### 1、文本框（Textarea）

- 可以通过`rows`属性改变行数

```html
<form role="form">
    <div class="form-group">
        <textarea class="form-control" rows="3"></textarea>
    </div>
</form>
```

#### 2、checkbox和radio

- 使用类`.checkbox`和`.radio`处理控件和标签的样式
- 使用类`.checkbox-line`和`radio-inline`将控件变成内联

```html
<form>
    <div class="checkbox">
    	<label>
            <input type="checkbox">记住密码
        </label>
    </div>
    <div class="radio">
        <label>
        	<input type="radio">测试
        </label>
    </div>
</form>
```

```html
<form>
    <div class="form-group">
    	<label class="checkbox-inline">
            <input type="checkbox">记住密码
        </label>
    </div>
    <div class="form-group">
        <label class="radio-inline">
        	<input type="radio">测试
        </label>
    </div>
</form>
```

#### 3、表单控件状态

##### (1) 焦点状态

- `Bootstrap`有获取焦点状态的定制样式，给控件添加类`form-control`即可使用样式；

##### (2)禁用状态

- `Bootstrap`有禁用状态的定制样式，相应的控件添加属性`disable`即可；
- `<fieldset>`中设置了`disabled`属性，整个域都被禁用；
- 被禁用的域`<fieldset>`中若有`<legend>`，`<legend>`内的控件虽然后禁用样式，但是禁用状态无效；
- 

##### (3)验证/提示状态

- 使用类`.has-warning/.has-error/.has-success`来定制验证状态；
- 使用`bootstrap`的免费图标，需要在控件下方添加`<span>`；
- 使用`.help-block`实现信息提示块；

示例：

```html
<form class="form-horizontal" role="form">
	<div class="form-group has-success has-feedback ">
		<label class="control-label" for="success">成功状态</label>
		<input type="text" class="form-control" id="success" placeholder="成功状态">
        <span class="help-block">输入正确</span>
		<span class="glyphicon glyphicon-ok form-control-feedback"></span>
	</div>
</form>
```

#### 4、按钮

- 先通过`.btn`类定义一个基础的按钮风格；
- 在使用`.btn-default`定义不同的按钮风格；
- 支持多种标签形式的按钮；
- 使用类`.btn-lg/sm/xs`定制按钮大小；
- 使用类`btn-block`类将按钮的内联属性变为块状属性；
- 可以使用类`.disabled`来定制禁用样式，但是这不影响行为；

示例：

```html
<button class="btn btn-default btn-lg">测试按钮</button>
<input type="button" class="btn btn-success btn-sm" value="测试按钮">
<input type="submit" class="btn btn-info btn-xs">
<div class="btn btn-warning btn-block">警告按钮</div>
<span class="btn btn-success">成功按钮</span>
```

#### 5、图片

- 使用`img-responsive/img-rounded/circle/thumbnail`类来定制图片

示例：

```html
<div class="col-md-4">
	<img alt="140*140" src="../images/test.png">
	<div>默认图片</div>
</div>
<div class="col-md-4">
	<img src="../images/test.png" class="img-responsive">
	<div>响应式图片</div>
</div>
<div class="col-md-4">
	<img src="../images/test.png" class="img-rounded">
<div>圆角图片</div>
</div>
<div class="col-md-4">
	<img src="../images/test.png" class="img-circle">
	<div>圆形图片</div>
</div>
<div class="col-md-4">
	<img src="../images/test.png" class="img-thumbnail">
	<div>缩略图</div>
</div>
```

#### 6、图标

- `Bootstrap`框架提供近200中不同的icon图片，都是使用CSS3的@font-face属性配合字体实现icon效果；

- 使用`.glphicon`类设置图标统一样式；
- 再使用`.glyphicon-search`等图标类使用相应的图标；

示例：

```html
<span class="glyphicon glyphicon-search"></span>
<span class="glyphicon glyphicon-plus"></span>  
```

