## （1）`onchange`事件

- `onchange`事件会在域的内容改变时触发；
- 支持标签：`<input type="text">`/`<textarea>`/`<select>`/`<keygen>`；
- ***元素值改变*** && ***失去焦点时触发***，且两次的值一样不会触发；
- 通过`js`该改变`DOM`的值不会触发；

```html
<input type="text" id="aa" onchange="function()">
```

```javascript
$("#aa").change(function(){
    doSomeThing();
});
```

## （2）`onpropertychange`事件

- `onpropertychange`在元素属性改变时触发；
- 当元素`disable=true`时不触发；
- 只在IE下支持，其他浏览器可用`oninput`解决；

```html
<input type="text" onpropertychange="function()">
```

## （3）`oninput`事件

- 在`<input>`/`<textarea>`的值改变时触发，无需等焦点失去；
- `H5`事件，用于检测文本输入类控件值的改变；
- 从脚本修改不会触发；
- 从浏览器下拉框改变取值时不会触发；
- IE9以下不支持；

```html
<input type="text" id="aa" oninput="function()">
```

```js
$("#aa").on("input propertychange", function(){
    doSomeThing();
});
```

## （4）`addEventListener`事件

- 用于向指定元素添加事件；
- IE9以下不支持，可用`attachEvent`代替；

```js
element.addEventListener(event, function, useCapture);
```

