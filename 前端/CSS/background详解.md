# 所有属性

| 属性                  | 描述                                |
| --------------------- | ----------------------------------- |
| background-color      | 背景色                              |
| background-postion    | 背景图像位置                        |
| background-size       | 背景图片尺寸                        |
| background-repeat     | 如何重复背景图像                    |
| background-origin     | 背景图片定位区域                    |
| background-clip       | 背景绘制区域                                    |
| background-attachment | 背景图片是否固定/随着页面滚动而滚动 |
| background-image      | 背景图像                            |

## `background-postion`属性

| 可能的值      | 描述 |
| ------------- | ---- |
| top/center/bottom left/center/right      | 垂直位置 水平位置 |
| x% y%         | 水平百分比 垂直百分比 |
| xpos ypos     | 水平位置 垂直位置 |

- %和`px`可以混用；
- 只规定一个，另一个默认`center`，即50%；



## `background-repeat`属性

| 可能的值  | 描述                       |
| --------- | -------------------------- |
| repeat    | 默认，垂直和水平方向都重复 |
| repeat-x  | 水平方向重复               |
| repeat-y  | 垂直方向重复               |
| no-repeat | 不重复                     |
| inherit   | 继承                       |

- 重复即当图像未填满区域时，未填满的部分如何显示；



## `background-origin`属性

| 可能的值    | 描述             |
| ----------- | ---------------- |
| padding-box | 相对于内边距定位 |
| border-box  | 相对于边框定位   |
| content-box | 相对于内容定位   |



## `background-clip`属性

| 可能的值    | 描述           |
| ----------- | -------------- |
| border-box  | 被裁剪到边框   |
| padding-box | 被裁剪到内边距 |
| content-box | 被裁剪到内容   |



## `background-attachment`属性

| 可能的值 | 描述                       |
| -------- | -------------------------- |
| scroll   | 默认，随着页面的滚动二滚动 |
| fixed    | 页面滚动时，背景不移动     |
| inherit  | 继承                       |

