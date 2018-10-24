- 网格布局，将容器分成12份（可调，重新编译LESS/SASS源码）；

#### 网格常用类

| 类名       | Description                   |
| ---------- | ----------------------------- |
| .container | 容器，bootstrap网格布局基于此 |
| .row       | 行                            |
| .col-xs-4  | 超小屏幕（<768px，比如手机），4列，小于12 |
| .col-sm-4 | 小屏幕（768px<=，比如平板），4列 |
| .col-md-4 | 中等屏幕（992px<=，PC桌面），4列 |
| .col-lg-4 | 大屏幕（1200px<=，大桌面显示器），4列 |
| .col-*-offset-4 | *分xs/sm/md/lg，偏移4列，大于1，小于11 |
| .col-*-push-4 | *分xs/sm/md/lg，向右移动4列，大于1，小于11 |
| .col-*-pull-4 | *分xs/sm/md/lg，向左移动4列，大于1，小于11 |

###### `.col-*-push-n`和`.col-*-offset-n`的区别：

- offset会挤压后面列，导致后面列位置改变；

- push不会挤压后面列，后面列可能和push列重叠；

#### 基本结构

```html
<div class="container">
    <div class="row">
        <div class="col-md-4"></div>
        <div class="col-md-4"></div>
    </div>
</div>
```



