### 一、查找祖先元素 -- 向上遍历

```javascript
$(selector).parent()	//返回直接父元素
$(selector).parents(filter)	//返回所有祖先元素，直到文档根元素(<html>)
$(selector).parentUntil(element, filter)//返回和element之间的所有祖先元素
```

示例：

```javascript
$("p").parentUntil("div")		//返回<p>和<div>之间的所有祖先元素
$("p").parentUntil("div", "ul")	//返回<p>和<div>之间的所有<ul>祖先元素
```

### 二、查找后代元素 -- 向下遍历

```javascript
$(selector).children()	//返回直接子元素
$(selector).find(filter)//在当前元素的子元素中查找第一次出现的filter元素
```

### 三、查找同胞元素 -- 水平遍历

- 同胞拥有相同的父元素；

```javascript
$(selector).siblings([filter])	//返回所有同级元素
$(selector).next()				//返回下一个同级元素
$(selector).nextAll([filter])	//返回后面所有的同级元素
$(selector).nextUntil([filter])	//从当前元素向下查找，直到下一个元素之间的所有同级元素

$(selector).prev()				//返回上一个同级元素
$(selector).prevAll([filter])	//返回前面所有的同级元素
$(selector).prevUntil([filter])	//从当前元素向上查找，直到下一个元素之间的所有同级元素
```

### 四、过滤

- 在一组元素中查找符合的元素

```javascript
$(selector).first()	//返回被选元素中的第一个元素
$(selector).last()	//最后一个
$(selector).eq(index)	//返回被选元素中的滴index个元素
$(selector).not([filter])	//返回被选元素中查找被过滤掉的元素
```

示例：

```javascript
$("p").not(".intro");	//返回<p>内部的所有class!=intro的元素
```

