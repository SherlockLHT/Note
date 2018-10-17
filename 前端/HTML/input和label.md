##### `<label>`可以作为`<input>`标签的文字标注

当选择`<label>`时，焦点会转到和其相关联的`<input>`控件上

## 标签关联的两种方式

#### （1） `for`属性和`id`属性

`label`标签的`for属性`和`input`的`id`属性值相等，则可关联

示例：

```html
<label for="name"></label>
<input type="text" id="name">
```

#### （2） `label`包含`input`

- 点击`label`时，点击事件会响应两次，点击`label`本身一次，`input`触发后冒泡到`label`上再触发一次

```html
<label>
	<input type="text">
</label>
```

