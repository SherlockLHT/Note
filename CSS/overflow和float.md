- 当内容溢出时的判定；

### 一、可能的值

|  value  | description |
| :-----: | :---------: |
| visible | default，内容不被修剪，显示在元素框之外 |
| hidden | 内容被修剪，被修剪的内容不可见 |
| scroll | 显示滚动条，内容被修剪，被修剪的内容通过滚动条拖动可见 |
| auto | 当内容溢出时，同scroll |
|inherit| 从父类继承 |

注：若元素没有设定尺寸，则元素会根据内容大小动态调整尺寸；

### 二、overflow: hidden和float浮动的自适应问题

- `overflow: hidden`不仅仅能隐藏溢出，还有能清除浮动；
- 当使用float之后，box的高度不会被被浮动的对象影响了，即此时，浮动的元素已经脱离了box了；
- 当使用`overflow:hidden`之后，浮动的元素再次进入box；

