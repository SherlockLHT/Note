**布局方式类似于bootstrap，通过基础的24分栏，创建布局**

**element-ui依赖于vue，即对应的组件，需要new vue之后，才会有效果**

## 基础布局

```html
<el-row>
	<el-col :span="24"><div class="div"></div></el-col>
</el-row>
<el-row :gutter="20">
	<el-col :span="12"><div class="div"></div></el-col>
	<el-col :span="12"><div class="div"></div></el-col>
</el-row>
```

- `el-row`定义一行，其中`:gutter`属性指定每列之间的间隔，默认为0；
- `el-col`定义一列，其中使用`:span`设置列数，最多24列，超出整体无效；

| 属性 | 描述 |
| ---- | ---- |
| :gutter | 列间隔 |
| :span | 指定占据列数 |
| :offset | 向右偏移列数 |

## 对齐方式

```html
<el-row type="flex" justify="start" :gutter="20">
	<el-col :span="6"><div class="div"></div></el-col>
	<el-col :span="6"><div class="div"></div></el-col>
	<el-col :span="6"><div class="div"></div></el-col>
</el-row>
```

- 将`el-row`的`type`属性设置成`flex`，可以使用flex布局；

- 通过`justify`属性指定布局方式；

  |     属性      |     描述     |
  | :-----------: | :----------: |
  |     start     | 左对齐(默认) |
  |    center     |     居中     |
  |      end      |    右对齐    |
  | space-between |   两边对齐   |
  | space-around  |   分散对齐   |


