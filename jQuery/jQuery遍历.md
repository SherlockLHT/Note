### 一、祖先元素

```javascript
$(selector).parent()	//返回直接父元素
$(selector).parents(filter)	//返回所有祖先元素，直到文档根元素(<html>)
$(selector).parentUntil(element, filter)//返回和element之间的所有祖先元素
```

示例：

```javascript
$("p").parentUntil("div")
$("p").parentUntil("div", "ul")
```

### 二、后代元素

```javascript
$(selector).children()	//返回直接子元素
$(selector).find()		//返回所有直接子元素
```

