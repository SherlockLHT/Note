# 需求

- 在页面上新建菜单，并通过点击菜单打开使用qweb渲染的自定义页面；
- qweb渲染的数据使用controller获取；

# 实现

**步骤：**

1. 新建视图，在需要的地方添加菜单项，并添加执行的action，使用 **ir.actions.client** 进行自定义页面的跳转动作；
2. 编写qweb模板；
3. 添加js文件，定义客户端实现，并注册到action中，并使用 **session.rpc()** 方法跳转到controller；
4. 添加视图xml，将js引入odoo；
5. 在 **ir.actins.client** 中使用 **tag** 指定js中注册的客户端动作；
6. 添加controller，获取rpc url，获取数据，并使用json格式返回；
7. 将添加的qweb模板添加到qweb列表中，视图xml添加到data列表中，

## 1、添加菜单和action

```xml
<odoo>
    <data>
        <record id="pam_equip_action" model="ir.actions.client">
            <field name="name">设备统计</field>
            <field name="tag">pam_statistics</field>
        </record>

        <menuitem id="pam_test_menu" name="qweb测试" action="pam_equip_action"
                  parent="pam.category_menu_mainform_2" sequence="3"/>
    </data>
</odoo>
```

## 2、编写qweb模板

```xml
<?xml version="1.0" encoding="UTF-8"?>
<templates id="pam_statistics_template" xml:space="preserve">
	<t t-name="pam_statistics_page">
		<style type="text/css">
			.outer{
			}
		</style>
		<div class="outer">
		</div>
	</t>
</templates>
```

- qweb模板中可以添加自定义css类；

## 3、添加js文件

```javascript
odoo.define('pam_statistics', function (require) {
    "use strict";

    var core = require('web.core');
    var Model = require('web.Model');
    var Widget = require('web.Widget');
    var data = require('web.data');
    var session = require('web.session');

    var demo = Widget.extend({
        template: 'pam_statistics_page',
        init: function (parent, action) {
            this._super(parent);
            this._super.apply(this, arguments);
            this.action = action;
        },

        start: function(){
            session.rpc("/web/object_type", {}).then(function(result){
                //正确得到controller返回的数据，进行页面渲染
            }, function(){
                //未能正确得到controller返回的数据，错误处理
            });
        }
    });

    core.action_registry.add('pam_statistics', demo);	//将动作添加到action中
    return demo
});
```

**说明：**

- 定义 **Widget.extend** 类型的客户端数据，**init()** 和 **start()** 方法可以进行一些数据处理；
- 使用 **core.action_registry.add()** 方法将视图添加到action中，并制定名称；
- 将名称添加到action的 **tag** 标签中；

## 4、添加视图，添加js文件到模块中

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
	<data>
		<template id="assets_backend" name="custom page assets" 		inherit_id="web.assets_backend">
			<xpath expr="//script[last()]" position="after">
				<script type="text/javascript" src="/pam/static/src/js/action.js" />
				<script type="text/javascript" src="/pam/static/src/js/test.js" />
			</xpath>
		</template>
	</data>
</odoo>
```

## 5、添加controller进行数据的获取和返回

```python
# -*- encoding: utf-8 -*-
from odoo import http

class Route(http.Controller):
    @http.route(['/web/object_type'], type='json', auth='public')
    def getEquipByObjectType(self):
		# doSomething
        return result
```

