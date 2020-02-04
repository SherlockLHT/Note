<font size = 3>

# 说明

odoo中的视图是可以继承的，与其说是继承，不如说是扩展，因为继承视图最终会改变原来的视图

# 普通视图继承

## xpath

视图的继承使用 **xpath** 改写元素

**xpath** 使用 **expr** 和 **position** 属性来改写元素

## expr属性

- expr用于定位元素节点；
- 若匹配到了多个，则以第一个为准，即匹配到了就不会再继续往下匹配；

语法如下：

```xml
expr="//标签名[@属性='属性值']"
```

示例：

```xml
expr="//field[@name='value']"
```

该句表示：查找field标签，且标签的name属性的值为value，即若出现下面标签，则会被匹配到

```xml
<field name="value" />
```

## position属性

position决定如何改写元素，默认 **inside**，可能的值包括：

| 值         | 说明                                                 |
| ---------- | ---------------------------------------------------- |
| after      | 将内容添加在匹配到的节点之后                         |
| before     | 将内容添加在匹配到的节点之前                         |
| inside     | 默认值，将内容追加到匹配的节点里面                   |
| replace    | 替换匹配到的节点，如果用空内容替换，则表示删除该节点 |
| attributes | 修改匹配到的节点的属性                               |

**attributes** 用法和其他几个不同，说明如下：

```xml
<attribute name="属性名">属性值</attribute>
```

示例：

```xml
<xpath expr="//field[@name='value']" position="attributes">
	<attribute name="nolabel">1</attribute>
</xpath>
```

上例中，修改后的元素变为：

```xml
<field name="value" nolabel="1" />
```

## 指定继承模型

使用 **inherit_id** 来指定继承模型的id

示例：

```xml
<record id="view_tree_inherited" model="ir.ui.view">
	<field name="name">视图名</field>
	<field name="model">模型名</field>
	<field name="inherit_id" ref="被继承的视图id"/>
    <!-- 在arch中进行扩展 -->
	<field name="arch" type="xml">
		<field name="属性名" position="更改位置">
			<field  />
		</field>
	</field>
</record>
```

# QWeb继承

QWeb的继承和普通视图类似，都是匹配节点，然后更改

QWeb分为两种：

- 一种是视图模板，多写在 **static/src/xml** 中，文件名添加在 **qweb** 列表中，一般用html+qweb语句编写；

- 一种是data类型，多写在 **views** 目录下，文件名添加在 **qweb** 列表中，只是一些辅助界面，大多用于定义、引入css文件等；

## 视图模板类QWeb

- 继承的qweb一般和被继承的放在一起；
- 使用 **t-query** 定位，使用 **t-operation** 修改；

示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<template id="模板id" xml:space="preserve">
    <t t-extend="要继承扩展的template的name属性值">
        <t t-jquery="使用选择器来定位" t-operation="相当于position">
            //扩展的内容，一般用html语法+qweb语句编写
        </t>
    </t>
</template>
```

## data类型QWeb

- 继承的qweb一般和被继承的放在一起；
- 使用 **xpath** 定位修改

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <template id="模板id" inherit_id="模块名.需要继承的template_id">
        <xpath expr="xpath定位语句">
            <!--
				引入的内容，一般是link标签和script标签，
				把static/src目录下的css、js子目录的文件引入
			-->
        </xpath>
    </template>
</odoo>
```

