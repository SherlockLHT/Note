<font size=4>

[TOC]

# 一、说明

有时候我们需要在odoo中添加自定义的页面，这部分的实现方式有很多种，比如使用qweb模板，也可以利用H5的 **\<iframe>** 标签嵌入页面

使用qweb模板添加自定义页面可以参考文章 ***odoo中嵌入qweb模板页面*** 一文，本文简单介绍使用 \<iframe> 标签的方法

# 二、\<iframe>

\<iframe> 标签一般用于创建一个文档的内联框架，除了一些特别古老的浏览器外，主流的浏览器都支持该标签

具体说明参考：https://www.cnblogs.com/hq233/p/9849939.html

通常使用 \<iframe> 标签的方法是，直接在页面嵌套 \<iframe>，并制定其 src 属性即可：

```html
<iframe src="index.html"></iframe>
```

当然，如果高端一些，可以设置一些 \<iframe> 的属性

|    属性     | 描述                                                         |
| :---------: | ------------------------------------------------------------ |
| frameborder | 是否显示边框，1(yes),0(no)                                   |
|   height    | 框架作为一个普通元素的高度，建议在使用css设置                |
|    width    | 框架作为一个普通元素的宽度，建议使用css设置                  |
|    name     | 框架的名称，window.frames[name]时专用的属性                  |
|  scrolling  | 框架的是否滚动。yes,no,auto                                  |
|     src     | 内框架的地址，可以使页面地址，也可以是图片的地址             |
|   srcdoc    | 用来替代原来HTML body里面的内容。但是IE不支持， 不过也没什么卵用 |
|   sandbox   | 对iframe进行一些列限制，IE10+支持                            |

# 三、在odoo中嵌入iframe

利用 iframe 元素的特性，可以再odoo中嵌入前端的页面

方法和使用qweb嵌入自定义页面类似，只是在 qweb 那一步骤中，不再使用 qweb 的语法写复杂的页面，而是简单的添加一个 iframe 标签，并在js中对此标签进行操作，比如修改 iframe 标签的 src 属性，即可嵌入页面，还可修改 srcdoc 属性，直接添加静态页面

## 示例：

在odoo中嵌入动态页面，具体步骤和添加qweb类似

**步骤：**

1. 创建action和menuitem，并将菜单关联到action上，使用 **ir.actions.client**，在tag属性中指定客户端动作名；
2. 编写qweb模板，模板内只加 iframe 标签；
3. 添加 js 文件，定义客户端的实现，并注册到action中的客户端动作名中，并在 js 中使用 **session.rpc()** 方法跳转到 controller；
4. 添加视图 xml，将 js 文件引入odoo模块；
5. 添加 controller，获取 js 中的rpc url，获取数据，处理并返回；
6. 将qweb模板添加到qweb列表中，将视图xml添加到data列表中；

### 1、添加action和menuitem

```xml
<?xml version="1.0" encoding="utf-8" ?>
<odoo>
    <record id="iframe_test_view_222" model="ir.actions.client">
        <field name="name">动态页面</field>
        <field name="tag">my_test_iframe_dynamic</field>
    </record>

    <menuitem name="嵌入动态页面" id="test_oframe_view_menu_2"
              action="iframe_test_view_222" parent="ep_project_manger_main_menu"/>
</odoo>
```

**说明：**

1. 使用 **ir.actions.client** 实现；
2. 在 **tag** 属性中制定动作标签，js完成动作之后，要使用此标签注册到action中；

### 2、编写qweb模板

```xml
<?xml version="1.0" encoding="UTF-8"?>

<templates xml:space="preserve">
    <t t-name="test_iframe_view">
        <iframe id="iframe1111"  marginheight="0" marginwidth="0" width="100%"
                 height="100%" frameborder="0" allowfullscreen="True">
        </iframe>
    </t>
</templates>
```

**说明：**

1. 定义模板，使用 **t-name** 指定唯一模板名，具体可参考qweb相关文章，js 中需要将创建的Widget管和模板关联起来；
2. 在模板中添加 iframe 标签，并指定唯一标签id，js中需要使用此标签修改其属性；

### 3、编写js文件

```javascript
odoo.define('test_iframe', function (require) {
    "use strict";

    var core = require('web.core');
    var Widget = require('web.Widget');
    var Model = require('web.Model');
    var session = require('web.session');
    var PlannerCommon = require('web.planner.common');
    var framework = require('web.framework');
    var webclient = require('web.web_client');
    var PlannerDialog = PlannerCommon.PlannerDialog;

    var QWeb = core.qweb;
    var _t = core._t;
    var lcontext=null;
    var context=null;
    var Dashboard = Widget.extend({
        template: 'test_iframe_view',

        init: function (parent, data) {
            this.data = data;
            this.parent = parent;
            context=this.data.context;
			lcontext=this.data.context.search_default_url;
            return this._super.apply(this, arguments);
        },

        start: function () {
            var self = this;
            var loading_done = new $.Deferred();

            session.rpc("/iframe/dynamic", {}).then(function (data) {
                var iframe = document.getElementById("iframe1111");
                iframe.src = data;	//修改iframe的src属性实现页面的嵌套
                //iframe.srcdoc = data;	//如果得到的页面的内容，可以使用srcdoc属性嵌入

            }, function(){
                console.log('not response');
            });

            return true;
        },
    });

    core.action_registry.add('my_test_iframe_dynamic', Dashboard);

    return {
        Dashboard: Dashboard,
    };
});
```

**说明：**

1. 创建 widget，并关联模板；
2. 使用 **core.action_registry.add()** 方法将widget注册到action中；
3. 使用 **session.rpc()** 方法和controller进行数据交互；
4. 根据 iframe 的 id 修改其属性；

### 4、添加视图，将js文件添加到模块中

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <template id="assets_web_report_backend" name="Web Settings Dashboard Assets" inherit_id="web.assets_backend" priority="18">
        <xpath expr="." position="inside">
            <script type="text/javascript"
                    src="/electricity_protect/static/js/test_iframe.js"></script>
        </xpath>
    </template>
</odoo>
```

### 5、添加controller进行数据的获取和处理返回

```python
# -*- coding: utf-8 -*-

from odoo import http
from jinja2 import Environment, FileSystemLoader
import os

BASE_DIR = os.path.dirname(os.path.dirname(__file__))
templateLoader = FileSystemLoader(searchpath=BASE_DIR + "/templates")
env = Environment(loader=templateLoader)

class desk_web(http.Controller):
    
    @http.route('/desk/static', auth="public", type='json')
    def desk_desk(self, **post):
        env.get_template('index.html')
        template = env.get_template('index.html')
        return template.render()	#直接返回静态网页的内容

    @http.route('/iframe/dynamic', auth="public", type='json')
    def dynamic(self, **post):
        return "https://www.baidu.com"
```



