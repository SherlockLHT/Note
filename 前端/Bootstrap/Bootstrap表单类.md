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
| .input-sm/lg | 适用于`<input>/<textarea><select>`，控制控件高度（更小/更高） |
| .has-warning | 用于`<div>`，提供验证效果，警告状态（黄色） |
| .has-error | 错误状态（红色） |
| .has-success | 成功状态（绿色） |
| .has-feedback | 表单验证中添加icon |

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

- `Bootstrap`有禁用状态的定制样式，响应的控件添加属性`disable`即可；
- `<fieldset>`中设置了`disabled`属性，整个域都被禁用；
- 被禁用的域`<fieldset>`中若有`<legend>`，`<legend>`内的控件虽然后禁用样式，但是禁用状态无效；

##### (3)验证状态

