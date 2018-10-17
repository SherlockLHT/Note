## position制定元素的定位类型

参考文章：https://www.cnblogs.com/theWayToAce/p/5264436.html

| value    | Description                                               |
| -------- | --------------------------------------------------------- |
| static   | 静态定位，默认值，没有定位/正常定位，元素出现在正常的流中 |
| relative | 相对定位，相对正常位置的相对位置，正/负                   |
| fixed    | 固定定位，相对浏览器位置固定                              |
| absolute | 绝对定位，相对最近的父元素的相对位置                      |
| sticky   | 相对于用户滚动的位置定位                                  |

## static - 静态

缺省设置，没有定位，元素出现在正常的流中，忽略`top/bottom/left/right/z-index`声明。

## fixed - 固定定位

==相对浏览器窗口==位置固定，即使窗口滚动（文档滚动）也不会移动
可以看成包含块是浏览器视窗，和`absolute`的区别在于包含块不同

## absolute - 绝对定位

相对于包含块（最近的postion值）进行定位，即相对于==第一个父元素==进行定位
相对最近的包含块（最近的postion值）的相对位置，没有已定位的父元素，则相对html
注：只有当包含块（最近的postion值）的`postion`的值为`relative`时，相对定位才有效

## relative - 相对定位

相对于==本元素的正常位置==进行定位，使用`top/bottom/left/right/z-index`进行定位。

## sticky

相对于用户滚动的位置定位，在`relative`和`fixed`之间切换，超出阈值则切换定位方式 `top/right/bottom/left`四个阈值至少指定一个，否则，同`relative`

## 重叠元素

元素定位和文档流无关，可以互相覆盖` z-index`属性指定元素的堆叠顺序，值大的在上面