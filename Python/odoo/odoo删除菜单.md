<font size = 4>

odoo中不改动原始代码删除菜单的方法有两种

## 法一

使用 \<delete\> 删除菜单

```xml
<delete model="ir.ui.menu" id="hr.menu_id" />
```

id 属性传入需要删除的菜单id即可

## 法二

利用权限隐藏菜单

```xml
<record id="menu_invisible" model="res.groups">
    <field name="name">Invisible</field>
 </record>
 <record model="ir.ui.menu" id="hr.menu_id">
    <field name="groups_id" eval="[(6,0,[ref('menu_invisible')])]"/>
</record>
```

新建一个用户组，并将菜单id配置到改组内，但是不给该组任何权限即可