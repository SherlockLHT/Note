#  说明

- odoo是一个高度数据驱动的系统
- 其中的一部分数据可以在加载的时候添加到数据中

# 示例

```xml
<?xml version="1.0" encoding="utf-8" ?>
<odoo>
    <data>
        <record id="data_1" model="test_module.model">
            <field name="name">1</field>
            <field name="value">111</field>
            <field name="description">描述</field>
        </record>
        <record id="data_2" model="test_module.model">
            <field name="name">2</field>
            <field name="value">222</field>
            <field name="description">描述2</field>
        </record>
    </data>
</odoo>
```

添加完数据文件后，必须将其加入到 **\_\_manifest\_\_.py** 文件中的 **data** 或者 **demo** 列表中

若加入到data中，则改数据始终加载

若加入到demo中，则仅在演示模式下加载

下面的代码示例，只截取其中相关的一部分

```python
...
	# always loaded
	'data': [
        'demo/demo.xml',
    ],
    # only loaded in demonstration mode
    'demo': [
    ],
...
```

# 详解

在《数据模型》一文中，创建的示例数据模型有name、value和description三个字段，上面代码则在加载的时候，添加数据

- 数据必须包含在**odoo**、**data** 节点内
- 一个 **record** 表示一个模型，**id** 唯一，**model** 必须是该数据需要添加的数据模型的名称
- **field** 添加字段的值，**name** 属性指定哪个字段

