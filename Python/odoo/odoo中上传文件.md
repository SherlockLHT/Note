# 一、上传附件并显示附件名

可用于上传单个文件

py文件中除了附件字段外，还需要一个字段保存附件名

**py文件：**

```python
report = fields.Binary(string=u'检测报告', attachment=True)
report_file_name = fields.Char()
```

**xml：**

```xml
<field name="report" filename="report_file_name"/>
<field name="report_file_name" invisible="1"/>
```

效果如下：

![1572494132806](1572494132806.png)

![1572494110307](1572494110307.png)

# 二、上传多个附件，并在form视图中显示文件列表

需要新建数据模型，在模型中定义文件的一些属性

**files.py**

```python
class HazardFile(models.Model):
    _name = 'test.file'
    _description = u'文件'

    data_id = fields.Many2one('test.data', string=u'数据')

    file = fields.Binary(string=u'文件', attachment=True)
    datas_fname = fields.Char()

    file_type = fields.Char(string=u'文件类型')
    file_content = fields.Char(string=u'文件内容')
    uploader = fields.Char(string=u'上传者')
    upload_time = fields.Datetime(string=u'上传时间', default=fields.Date.today)
```

**data.py**

```python
files = fields.One2many('test.file', 'data_id', string='附属资料')
```

**data.xml**

以下xml是在form视图中插入列表

```xml
...
<field name ="hazard_files" nolabel="1" 
       options="{'no_create': True, 'no_open': True}">		
    <tree editable="bottom">
		<field name="file" filename="datas_fname"/>
		<field name="datas_fname" invisible="1"/>
		<field name="file_type"/>
		<field name="file_content"/>
		<field name="uploader"/>
		<field name="upload_time"/>
	</tree>
</field>
...
```

# 三、上传文件并显示预览

可用于图片，可以再form视图中预览

**py文件：**

```python
photo = fields.Many2many("ir.attachment", string ="图片")
```

- 这里使用了 **ir.attachment** 模块；
- 一定要是 **Many2many**；

**xml文件：**

```xml
<field name ="test_photo" widget="many2many_binary"/>
```

则可以上传多个文件，效果如下：

![1572493862243](1572493862243.png)

# 四、上传多个附件

参考：https://blog.csdn.net/m0_37826101/article/details/101370346

二中的方法使用上传附件，并在视图中使用二进制显示的方法上传图片，但是一个模型有多个字段，则会出现某个字段添加的附件影响了其他字段的数据的情况。

也就是说，默认一个模型绑定一个附件列表

如果要上传多个附件，则可以如下的方法：

**py文件：**

```python
photos_1 = fields.Many2many(
	'ir.attachment',
	string=u'照片1',
	relation='hazard_photos_res_att_rel',
	column1='id',
	column2='att_id'
)
photos_2 = fields.Many2many(
	'ir.attachment',
	string=u'照片2',
	relation='process_photos_res_att_rel',
	column1='id',
	column2='att_id'
)
photo_3 = fields.Many2many(
	'ir.attachment',
	string=u'图片3',
	relation='check_photo_res_att_rel',	
	column1='id',
	column2='att_id'
)
```

**说明：**

- Many2many的第一个参数需要关联附件模型 **ir.attachment**；
- **relation** 表示关联表，自定定义即可；
- **column1** 表示附件id列；
- **column2** 表示ir.attachment附件的id列；

**xml:**

```xml
<field name ="photos_1" widget="many2many_binary" string="照片1"/>
<field name ="photos_2" widget="many2many_binary" string="照片2"/>
<field name ="photo_3" widget="many2many_binary" string="照片3"/>
```

这样可以保证三个附件不会相互影响