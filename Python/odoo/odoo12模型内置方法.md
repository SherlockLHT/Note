<font size = 4>

# 说明

odoo10的model都继承自 **odoo.models.Model**，Model类中定义了一些方法，如果需要也可以重写，odoo12有一种套方法，可在类下查看

# 模型层面

## 1、_table_exist

检查该模型对于的数据库表是否存在，是则返回1，否则返回0.

```
@api.model_cr
    def _table_exist(self):
        pass
```

# 模型记录层面

## 2、create(self,vals)

记录的创建函数，一般情况下，是根据视图传过来的dict对象，生成模型记录。

我们可以重写create方法：

1. 获取vals参数，从中提前数据进行校验、替换；
2. 用super(类，self).create(new_vals) 把新的dict作为参数执行记录创建。

## 3、write(self,vals)

记录的修改函数，很少重写，参数也是dict。

## 4、read(self,fields)

记录的查看函数，参数是查看哪些字段，默认是全字段，很少重写。

## 5、unlink(self)

记录的删除函数，参数是当前数据记录集。一般重写该函数，校验记录的状态等，限制某些记录不能被删除。

## 6、_search()

```python
_search(self, args, offset=0, limit=None, order=None, count=False, access_rights_uid=None)
```

   模型记录的搜索函数，定义了该模型的记录被关联搜索、搜索视图搜索时的**条数、排序字段、总数、检索权限**等。

## 7、name_get()

name_get()函数定义了该模型的记录在被关联、搜索时，所显示出来的名字，默认是使用name字段的值。

如果我们想自定义该模型的记录显示的名称，例如：使用 编码+name字段 显示的复合名称，则可以重写name_get()函数:

```python
@api.multi
@api.depends('用于拼接名称字段', 'name')
def name_get(self):
    """
    名称显示格式。
    """
    new_name = self.用于拼接名称字段 + self.name
    return [(self.id,new_name)]
```

## 8、name_search()

name_get()定义了记录如何显示，那么name_search()则定义了记录如何被查找。

name_search()定义了模型记录在被关联、被搜索时，通过输入的内容进行模糊查找的逻辑。

默认情况下，是通过查找记录的 name  字段值是否与输入内容类似进行比对查找，我们可以改写模型的name_search()函数，把更多的字段加入比对中去。

```python
@api.model
def name_search(self, name, args=None, operator='ilike', limit=查找条数):
    """
    名称模糊搜索。
    """
    args = args or []
    domain = []
    domain.append([(更多检索条件)])
    return super(类名, self).name_search(name, domain + args, operator=operator, limit=limit)
```

​        或直接查找，返回所得记录集的name_get:

```python
@api.model
def  name_search(self,name='',args=None,operator='ilike',limit=100):
    args = args or []
    domain = []
    if name:
        domain = ['|',('name',operator,name),('其他比对字段',operator,name)]
    pos = self.search(domain + args,limit=limit) #使用扩展后的条件进行查找
    return pos.name_get() #把查找结果的name返回
```

## 9、default_get()

default_get(fields) 函数用于初始化记录的默认值，对于模型的某些字段如果需要设置默认值，可以重写模型的default_get()函数达到目的。

例如：从表单中**携带上下文信息跳转到向导、跳转到一个模型的新建表单视图**等，可以在跳转时往context传递数据，然后在向导模型、被跳转创建的模型中重写default_get方法，从context中提前信息，进行字段默认值的初始化。

```python
@api.model
def default_get(self, default_fields):
   result = super(类名, self).default_get(default_fields)
   context_data = self.env.context.get('key')
   # 根据context_data进行相关数据查询、处理操作
   result.update({'字段': 默认值}) //更改记录的字段默认值
   return result
```

## 10、name_create(name)

相当于只传递name字段值，调用create方法创建一条新记录。

## 11、fields_get

字段查询函数,一般不重写：以数据字典的形式返回字段的定义，通过继承得来的字段也会在其中，string/help/selection属性会自动被翻译

```python
fields_get([fields]，[attributes])：
```

fields参数是字段列表、为空或不传返回所有字段
attributes 可指定字段的属性、为空或不传时返回全部的

# 视图信息层面

## 12、fields_view_get

视图查询函数，一般不重写：返回指定视图的具体组成如：字段，所关联的模型，视图结构。

```python
fields_view_get()：
    view_id 视图的id或None
    view_type 当view_id参数为空时指定视图类型如form,tree等
    toolbar 参数为true时将上下文动作包含在内
```

## 13、get_formview_action

表单视图获取函数，可以重写该函数，使模型加载某个特定的form视图，甚至可以在加载时传递context值，控制视图的行为。

## 14、load_views(views,options)

视图加载函数，可以重写该函数，在加载视图时传递context值，控制视图行为。