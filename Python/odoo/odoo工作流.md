# 说明

odoo的工作流实际使用 **Selection** 字段，并将字段显示在视图顶部，以达到显示流程的目的。

而实际的工作流程的改变，则是通过按钮，触发函数，改变字段的值

## 代码示例

### （1）py文件

```python
...
_inherit = ['mail.thread', 'ir.needaction_mixin']

state = fields.Selection([
	('new', u'创建'),
	('submit', u'待审批'),
	('confirmed', u'待执行'),
	('unconfirmed', u'驳回'),
	('cancel', u'撤回'),
	('done', u'关闭')
], default='new', string=u'任务状态', track_visibility='onchange')

@api.multi
def action_draft(self):
	self.state = 'draft'

@api.multi
def action_confirm(self):
	self.state = 'confirmed'

@api.multi
def action_done(self):
	self.state = 'done'
...
```

**说明：**

- **default** 表示初始状态；
- **track_visibility** 表示

```python
_inherit = ['mail.thread', 'ir.needaction_mixin']
```

则会记录字段的改变，并使用 **track_visibility** 字段制定哪个字段，所以，这里可以记录工作流字段的改变，以达到流程跟踪的目的

### （2）xml文件

```xml
...
<firld name="arch" type="xml">
    <form string="通用数据视图">
        <header>
			<button name="action_new" type="object" string="创建" 
                    states="new,submit" class="oe_read_only"/>
			<button name="action_submit" type="object" string="提交" 
                    attrs="{'invisible': [('state', 'not in', ('new','submit'))]}"
                    class="oe_highlight oe_read_only" confirm="是否确认提交?"/>
			<button name="action_cancel" type="object" string="撤回"
                    attrs="{'invisible': [('state', '=', 'submit')]}"
                    class="oe_read_only" confirm="确定撤回提交吗?"/>
			<field name="state" widget="statusbar"
                   statusbar_visible="new,submit,confirmed,done"/>
		</header>
        <div class="oe_chatter">
			<field name="message_ids" widget="mail_thread"/>
		</div>
    </form>
</firld>
...
```

**说明：**

- **name** 表示要触发的函数名，配合 **type** 为 **object** ；
- **string** 表示button显示的文字；
- button的显示控制有以下几种方式
  - **states** 控制，**states="new,submit"** 表示字段 **states** 为new/submit时显示；
  - **attrs** 修改 **invisible** 属性控制，内部是一个属性判断
- **widget** 表示，所指的字段以状态条的形式呈现
- **statusbar_visible** 可以控制状态条显示的字段
- 最后的 **div** 中即是要显示的流程跟踪
- **class** 表示样式，使用方式同H5，**oe_highlight** 表示高亮，**oe_read_only** 表示只读，即只在非编辑状态下可用；