## 问题描述

使用`CSS`定义样式，使用原生`JS`操作DOM，需要使用`js`获取`opacity`属性，操作方法如下：

```js
document.querySelecor("img").style.opacity;
```

理论上，应该获取到正确属性，但是每次都是空字符串

## 原因

***只有通过`HTML`代码写的内联样式，才能获取到***

`element.style`属性返回一个`css style declaration`对象，表示元素的内联style属性(attribute)，但是忽略样式表属性，所以无法操作

## 解决方法

### 方法一：

##### 将需要操作的属性作为内联样式卸载`HTML`中。

### 方法二：

##### 使用`window.getComputedStyle()`获取样式表属性

但是此方法获取的属性是只读的，就是说只能获取不能修改

```js
getComputedStyle(document.querySelector("img"), null).opacity;
```



