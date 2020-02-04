<font size=3>

[toc]

https://www.cnblogs.com/ygj0930/p/10826232.html

# 说明

- odoo中的五种action都是继承自ir.actions.actions模型实现的子类，共有五种。分别对应五种类型、五种用途。
- odoo中还有其他含有action命名的模型，诸如：action.todo等，都不是actions的子类，不是动作；
- odoo中翻译为动作的，也不全是action，例如：自动动作，它是ir.cron模型，执行服务器的定时任务。

# 一：窗口action(ir.actions.act_window )

​        最常用的action类型，用于 **打开模型的各种视图**。

## 字段列表：

**1：res_model**

​        需要在view里显示数据的model。

**2：view_type**

​        代表视图类型如：form,tree,gragh...，type参数列表的第一个会被默认用来展示。

**3：view_id**

​        数据库视图记录id或False，如果没有指定id，客户端会自动用fields_view_get()获取相应类型的默认视图。

**4：res_id (可选)**

​        当 **默认的视图类型是form，并且模型定义了不止一个form视图** 时，可用res_id字段指定加载具体的form视图id

**5：search_view_id(可选)**

​        (id, name)，id是储存在数据库的搜索视图id，默认会读取model的默认搜索视图

**6：target (可选)**

​        定义视图打开模式  在当前视图上打开(current)、使用全屏模式(fullscreen)、新窗口打开(new)，可使用main代替current来清除面包屑导航[面包屑导航：让用户了解目前所处位置，以及当前页面在整个网站中的位置]

**7：context (可选)**

​        额外的需要传给要打开的视图的环境数据。

```xml
<!-- 模型有多个同种视图时，指定打开具体的视图 -->
<field name="context">
    {'tree_view_ref':'模块.view_tree_XXX',
     'form_view_ref':'模块.view_form_XXX'}
</field>
<field name="search_view_id" ref="view_search_XXX />

<!-- 在跳转的同时启用过滤器 -->
<field name="context">
	{'search_default_过滤器名': active_id/True }
</field>

<!-- 传递数据，可用于domain中作为表达式的值 -->
<field name="context">
	{"key":value}
</field>

<!-- 指定跳转过去的视图记录的分组方式 -->
<field name="context">
	{'group_by': ['字段','...'];'group_by_no_leaf':1}
</field>
```

**8：domain (可选)**

​        自动添加到搜索视图中的查询条件，即：跳转到目标视图时，立即应用domain条件过滤模型记录。

​        表达式中的值可以是具体的常量值，也可以是调用该action时传进来的context中的变量值。

```xml
<field name="domain">[('字段', '=', '具体值'),('字段','=',上下文中的变量)]</field>
```

**context中的变量值有两种方式指定：**

**1）在python代码中调用action**

```python
ctx = self._context.copy()
ctx.update({
	'key': 值,
})
[action] = self.env.ref(action_name).read()
action['context'] = ctx
return action
```

**2）在action的context字段直接指定，不过一般都是明确的字面量值**

```xml
<!-- 递数据 -->
<field name="context">{"key":value}</field>
```

**9：limit (可选)**

​        在客户端显示的数据量，默认为80条。

**10：auto_search(可选)**

​        是否在加载默认视图后立即执行搜索，默认True

**11：view_mode**

​        以逗号分隔的视图类型列表，所有列举的类型的视图记录都会被加载。

**12：view_ids**

​        一般用于具体指定view_mode中列举类型的各种视图的具体记录。用法如下：

```xml
<record id="action_" model="ir.actions.act_window">
<field name="view_ids" eval="[(5,0,0),
                       (0,0,{'view_mode':'tree'}),
                       (0,0,{'view_mode':'form', 'view_id': ref('form视图id')})]"/>
</record>
```

# 二：链接Action(ir.actions.act_url)

可以通过odoo的链接打开一个网站页面，可通过两个字段来自定义：

- url -- 激活action时所打开的链接
- target，值可以有两种：
  - new：在新窗口打开
  - self：替换当前页面内容，默认new
### （1）视图上

通过点击菜单，打开链接

```xml
<record id="url_action_XXX" model="ir.actions.act_url">
	<field name="name"></field>
	<field name="url">网址</field>
	<field name="target">new</field>
</record>
<record id="base.open_menu" model="ir.actions.todo">
	<field name="action_id" ref="url_action_XXX"/>
	<field name="state">open</field>
</record>
```

###（2）python代码

可以作为按钮的点击函数，在函数中return一个链接action，打开链接

```python
return {
	'type': 'ir.actions.act_url',
	'url': 链接,
	'target': 'self',
	'res_id': self.id,
}
```

# 三：服务器Action (ir.actions.server)

可以通过服务器action来 **触发复杂的服务端动作：**

**id：**

​	服务器action在数据库存储的id

**context(可选) ：**

​	额外的传递给服务器action作用目标的数据

**model_id：**

​	与action相关联的model

**condition (可选)：**

​	使用服务端的 evaluation contexts 来执行python代码，如果是False则阻止action执行，默认值是True

**code：**

​	当调用action时执行的python代码

**object_create：**

​	使用钩子创建一条新记录（通过调用模型的create或copy方法）

​	**use_create**

1. new - 基于指定的 model_id创建一条记录

2. new_other - 基于指定的crud_model_id创建一条记录

3. copy_current - 复制action所引用的记录

4. copy_other - 复制一个通过ref_object获得的记录

   **fields_lines：**

​	当创建或复制记录时需要修改的字段，One2many 会有以下字段：

1. **col1：** 在use_create里所包含的需要被重赋值的 **ir.model.fields**

2. **value：**字段对应的值，基于type进行解析

3. **type：**取值value:就是value字段的值，取值equation：value字段会当成python来解析

   **crud_model_id：**

​	当use_create为new_other时，表示用于创建新记录的model id

- ref_object -- 当use_create为copy_other时用于指定创建记录时引用的记录
- link_new_record -- 是否用用link_field_id将新记录和当前记录进行many2one关联，默认False
- link_field_id -- 指定当前记录与新记录进行many2one关联的字段
- **object_write：**与object_create相似，不同的是 只修改当前记录而不创建新记录
- use_create
  1.current - 修改更新到当前记录
  2.other - 修改更新到通过crud_model_id 或 ref_object指定的新记录
  3.expression - 修改更新到通过crud_model_id 以及 write_expression筛选过后的记录
  - write_expression - 返回一条记录或对象id的python表达式
  - fields_lines,crud_model_id,ref_object与object_create一致
  - multi
    将通过child_ids many2many关系定义的action一个个执行，如果有action自己返回action，最后一个action被返回给客户端作为将前multi action的下一个action
  - trigger 发送一个信号给工作流
  - wkf_transition_id - 用于触发的与workflow.transition有Many2one关系的id
  - use_relational_model - 如果是base（默认），则触发当前记录的维护信号；如果是relational，则触发通过wkf_model_id 和 wkf_field_id筛选出来的当前记录的字段
  - client_action -- 返回通过action_id定义的action

​         用法举例：

```xml
<!-- 定义action -->
<record model="ir.actions.server" id="记录id">
        <field name="name"></field>
        <field name="type">ir.actions.server</field>
        <field name="model_id" ref="模块名.model_下划线分隔的模型名"/>
        <field name="code">
        要执行的python代码。
        </field>
 </record>

<!-- 调用action -->
<!-- 可以在界面上调用，作为按钮点击事件等 -->
<button name="%(模块名.action记录id)d" type="action" string="按钮文本" class="oe_link"/>
<!-- 也可以在python代码中使用 -->
<!-- 结合odoo中的定时任务使用 -->
```

# 四：客户端Actions (ir.actions.client)

​        触发一个**在客户端实现(即js文件中定义的函数，通过core.action_registry.add(tag,函数名) 注册到odoo中)动作：**

- tag -- action在客户端的标识符，一般是一个专用的字符串，在js文件中注册该动作时指定。
- params (可选) -- 用来传给客户端动作的，字典格式
- target (可选) -- current:当前内容区打开action；fullscreen:以全屏模式打开；new：以新窗口打开。
- context-- 作为额外数据，传递给客户端函数。

用法举例：

## 1）在js文件中定义客户端widget，并注册

```javascript
var 自定义widget名= Widget.extend({
    init：init函数；
    start:自动调用到start函数；
    其他函数,被init、start调用。//自定义widget，就是自定义动作
})
core.action_registry.add('widget tag名', widget名);
return {
    widget名: widget名,
};
```

## 2）在视图中调用：

**作为按钮的点击函数的name属性、作为菜单项的action**

```xml
<record id="action_" model="ir.actions.client">
	<field name="name"></field>
	<field name="res_model"></field>
	<field name="tag">widget注册时的tag名</field>
</record>
```

## 3）在代码中调用

```python
return {
	'type': 'ir.actions.client',
	'name': '',
	'tag': '动作的tag',
	'params': {key:value},
}
```

客户端动作十分强大而且自由，可以在js文件中使用前端逻辑定义一系列操作，诸如跳转、加载页面等等都可以。甚至，可以加载自定义的qweb页面进来，使用jinja填充数据，实现自由前端。

# 五：报表渲染Action (ir.actions.report.xml)

​    **此action用于在报表渲染前进行一些前置设定，如纸张大小、输出文件名等等：**

- name(必选) -- 在一个列表里进行查找时使用
- model (必选) -- 报表所反映的数据来源model
- report_type (必选) -- qweb-pdf | qweb-html
- report_name -- 报表命名，用于输出的pdf文件名
- groups_id -- 可以读取或使用当前报表的用户组，Many2many字段
- paperformat_id -- 报表所使用的纸张格式，默认使用公司的格式，Many2one字段
- attachment_use -- 当取值true的时候只在第一次请求时生成报表，之后直接从保存的报表打印，可用于生成后不会有改变的报表
- attachment -- 使用python表达式来定义报表名字，该记录可用变量object访问

​    用法举例：

## 1）定义报表模型

```python
class XXXReport(models.AbstractModel):
    _name = 'report.模块名.报表名'

    @api.model
    def get_data(self, 参数):
       获取报表所需数据并返回。

    @api.multi
    def render_html(self, docids, data=None):
        data = dict(data or {})
        data.update(get_data(参数))
        return self.env['report'].render('模块名.报表qweb文件template id', data)//传递data，渲染报表
```

## 2）定义报表视图

```xml
<?xml version="1.0" encoding="utf-8"?>
<odoo>
    <data>
        <template id="报表模板id">
            Qweb语法，定义报表格式。
        </template>
    </data>
</odoo>
```

## 3）定义报表打印action

```xml
<record id="" model="ir.actions.report.xml">
	<field name="name"></field>
	<field name="model">report.模块名.报表模型名</field>
	<field name="report_type">qweb-pdf|qweb-html</field>
	<field name="report_name">输出的报表文件名</field>
 </record>
```

## 4）在controller、按钮事件等地方，渲染报表

渲染时，会自动调用渲染action，按照action指定的纸张格式、输出文件名等设定进行渲染

```python
@http.route('/模块/xx_report', type='http', auth='user')
def print_xx_report(self, 查询条件值, **kw):
	#获取报表模型
	report_model = request.env['report.报表模型名']
	pdf = request.env['report'].with_context(查询参数 = 查询条件值).get_pdf(report_model, '模块.报表qweb文件的template id')
	pdfhttpheaders = [('Content-Type', 'application/pdf'), ('Content-Length', len(pdf))]
	return request.make_response(pdf, headers=pdfhttpheaders)
```

  注：我们创建的报表，都是report模型中的一条记录而已。

 因此，odoo报表打印其实就是report模型的两个方法：get_pdf(具体报表模型，报表视图模板id) 和 get_html(具体报表模型，报表视图模板id)。

因此，报表打印可以通过以上两个方法，可以在任何地方触发打印：在controller可以生成报表回传(如上）、也可以作为按钮的点击函数进行响应。

此外，report模型还提供了render方法，传递参数进来直接渲染报表的qweb文件，也是可以的。

```python
return self.env['report'].render('模块.报表template id', data)
```

## 5）报表的打印

上面四步只是生成了报表，但是要调起打印机打印，报表工作才算是完成。

PDF报表：由于生成了PDF文件，任何PDF阅读器都集成了打印选项，因此这不需要我们实现。

Html报表：网页渲染的报表，因为整个网页就是报表内容，因此打印报表就是打印网页。

1. 可以直接点击浏览器的“打印”菜单，进行打印；
2. 在报表页面，设置一个botton，为它指定响应事件，调用浏览器的打印函数

```javascript
$('.print_button').click(function() {window.print();})
```

 