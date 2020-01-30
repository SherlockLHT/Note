<font size=3>

[toc]

# 一、记录集

数据模型和数据库中的记录是通过记录集交互的

方法可以在记录集上执行，**self** 就是一个记录集

```python
class AModel(models.Model):
    _name = 'a.model'
    def a_method(self):
        self.do_operation()
```

但是如果选中多条记录，执行某个方法，则需要遍历 **self**

```python
def do_operation(self):
    for record in self:
        print record
```

## （1）字段的访问

- 字段直接当成类属性访问修改
- 修改字段会触发数据库的更新

## （2）记录的缓存和预取

odoo会在从数据库取某个字段的时候缓存其他字段，避免每次都读取数据库，造成性能损失

## （3）集合运算

**record in/not in set**：返回record是否在set中

**set1 </<= set2**：检查set1是否为set2的子集

**set1 >/>= set2**：检查set1是否为set2的超集

**set1 |/& set2**：返回set1和set2的并集/交集

**set1 - set2**：返回只包含set1，不包含set2的记录集

## （4）其他的记录集操作

**filtered()**：只返回符合提供的描述函数的记录

```python
#只保留当前的用户所在的公司的记录
records.filtered(lambda r: r.company_id == user.company_id)
```

这个限制也可以是某个字段的true/false

```python
#当partner_id.is_company为true时，记录保留
records.filtered("partner_id.is_company")
```

**sorted()**：返回的记录集按照提供的方法进行排序，如果没有提供排序方法，则使用默认排序

```python
#使用name字段进行排序
records.sorted(key=lambda r: r.name)
```

**mapped()**：根据传入的方法，返回记录集

![1573722613236](1573722613236.png)

```python
# 返回一个集合，集合的内容是选中的数据集的name字段和task_order字段拼接
result = self.mapped(lambda r: r.name + r.task_order)
```

![1573722634854](1573722634854.png)

这里的方法也可是是字符串，指明一个字段，则得到数据集中的该字段的集合

```python
#返回一个集合，集合的元素是选中的数据集的name字段
result = self.mapped('name')
```

![1573722769129](1573722769129.png)

# 二、Environment

**env** 保存用户ORM的各种具有上下文意义的数据，比如：数据库的游标（用于数据库的查询）、当前的用户（用于权限的检查）和当前的上下文（正在存储元数据），除此之外，也保存缓存。

所有的记录集都有不可变的上下文的环境，这些环境可以使用 **env** 进行读取，并提供对当前的用户（**user**）、数据库游标（**cr**）以及上下文（**context**）

```python
self.env
self.env.user
self.env.cr
```

![1573723597996](1573723597996.png)

**env** 还可以用于获取指定模块的记录

```python
self.env['res.partner'].search([('is_company', '=', True), ('customer', '=', True)])

```

上面代码则能获取 **res.partner** 模块中，字段 *is_company* 为True并且 *customer* 为True的数据

## Alter the Environment

可以在记录集中变更环境，变更之后则返回新的记录集数据。

**sudo()**：

在进行ORM操作前指定权限，若没有指定，则使用administrator（可以绕过权限），最后返回新的数据

```python
self.env['res.partner'].sudo(public).search([('is_company', '=', True)])
self.env['res.partner'].sudo().search([('is_company', '=', True)])

```

**with_context()**

**with_env()**

完全取代现有的环境

# 三、常用的ORM方法

## 1、search类

### search()

声明：

```python
search([], offset=0, limit=None, order=None, count=False)

```

- 使用domain进行数据的检索；
- 可以使用 **offset** 和 **limit** 参数控制数据子集，分别等同于sql语句的 **offset** 和 **limit**；
- 可以使用 **order** 参数进行按字段排序，等同于sql语句的 **ORDER BY**；
- **count** 默认False，如果是True，则返回匹配的记录的数量

```python
self.env['res.partner'].search([('is_company', '=', True)], limit=1)
self.env['res.partner'].search([('is_company', '=', True)], order='id')
self.env['res.partner'].search([('is_company', '=', True)], limit=2, offset=1)
self.env['res.partner'].search([('is_company', '=', True)], count=True)

```

### search_count()

如果只是检查是否有符合的数据，或者想要得到数据集的数量，使用之

### name_search()

声明：

```python
name_search(name='', args=None, operator='ilike', limit=100)

```

根据 **name** 字段搜索数据，如果没有定义name字段，则使用 **_rec_name** 指定的字段，即不仅返回的数据不仅要和name、operator匹配，还要和args内的domain匹配

改方法等同于：先用 **search()** 方法带参数args内的domain，再使用 **name_get()** 方法从结果集中获取数据

name：字符串，表示name字段需要匹配的内容

operator：匹配方式，比如like、=等

args：列表，额外的domain列表

## 2、CRUD类

### create()

创建数据集（插入数据），并且返回已经创建的数据

参数：包含模型字段值的字典

返回值：新创建的数据集

```python
self.env['pam.failure_type'].create({'name': 'test', 'desc': 'test'})

```

### write()

根据提供的参数update当前数据集的数据

参数：保存需要update的模型字段值的字典

返回值：无

```python
# 设置改模型electricity_protect.level的id为1的数据的name字段为测试write用法
self.env['electricity_protect.level'].browse(1).write({'name': '测试write用法'})

```

一对多和多对多的字段使用 **commands** 格式，这是一个三元组的列表，每个元素都是一条数据，元组示例：

```python
(0, _, values)	#使用values字典创建一条数据
(1, id, values)	#使用values更新数据id为id的数据
(2, id, _)		#移除数据id为id的数据，并在数据库中删除之
(3, id, _)		#移除数据id为id的数据，但不在数据库删除
(4, id, _)		#新增数据id为id的数据到数据集上
(5, _, _)		#等同于使用(3, id, _)依次移除每笔数据
(6, _, ids)		#等同于先用(5, _, _)，再依次使用(4, id, _)添加

```

### read()

从记录集中返回需要的字段数据

此方法从已知的记录集中返回字段数据，若原始的记录集为空，则最后返回的列表也为空

```python
#返回type.level中name字段包含'重要'字样的数据的id和name字段数据
self.env['type.level'].search([('name','ilike',u'重要')]).read(['name'])
self.env['type.level'].read(['name'])	#返回空列表

```

传参是一个数组，若不传，则返回全部字段数据

返回值是一个数组，每个元素的都是一个包含每个字段数据的元组

返回值默认都会带有id字段的数据，无论是否传递参数

### read_group()

根据分组字段，获取数据列表，分组字段必须是在列表视图中出现的，否则会抛出异常

函数声明：

```python
read_group(domain, fields, groupby, offset=0, limit=None, orderby=False, lazy=True)

```

domain：过滤条件列表，支持odoo风格的过滤

fields：需要获取的字段列表

groupby：分组字段列表，必须是列表视图中出现。它可以是一个字段，也可以是一个字符串

offset：偏移量，忽略一些数据

limit：返回数据集的最大数量

orderby：排序方法

lazy： bool型，如果是true，则按照groupby参数列表的第一个进行分组，其余的放在上下文键中；如果是false，则所有groupby一次完成分组

### browse()

根据一个或者多个数据库id获取数据，从当模块外获取数据的时候非常有用

```python
self.env['electricity_protect.level'].browse(2)
self.env['electricity_protect.level'].browse([2, 3, 4])

```

## 3、记录集操作

### exists()

返回数据库中包含该记录的记录集，可以用于是否存在的检测

```python
if not self.exists():
    raise Exception("The record has been deleted")

```

也可以用于在执行删除方法后调用检查是否删除

```python
#前面删除记录
record = self.exists() #检查记录是否删除

```

按照约定，新纪录当做存在的记录返回

### ids

非方法，从结果集中获取包含所有id的列表

```python
self.env['type.level'].search([('description', 'ilike', '等级')]).ids

```

### ensure_one()

验证当前记录集是否只包含单条记录

单条则返回该条记录集，否则抛出异常

### ref()

根据指定的xml的id获取记录，传参是 **模块名.xml id**

```python
result = self.env.ref('electricity_protect.ep_task_form_view').mapped('name')
#result的值为：

```

xml内容：

![1573781334104](1573781334104.png)

执行结果：

![1573781357857](1573781357857.png)

### unlink()

删除当前的记录

### filtered(func)

当参数func是true时，返回记录集

声明：

```python
filtered(func)

```

参数func：可以是一个函数，也可以是一个字段

### sorted()

记录集排序

```python
sorted(key=None, reverse=False)

```

参数：
key： 排序的参照字段

reverse： True时，倒序

### mapped(func)

参数可以是一个函数或者一个字段

若传入函数，则在self的所有记录集上执行func，并返回列表或者记录集

若传入字段，则返回包含该记录集数据的字段列表

## 4、环境交互

### sudo(user=SUPPERUSER)

记录集默认和当前的用户的权限有交互，使用sudo则使用user的的权限获取新的记录集，若使用默认参数，则使用超级用户，跳过所有的权限

```python
self.env['type.level'].sudo().search()
```

### with_context()

待整理

### with_env()

待整理

## 5、字段和视图查询

### fields_get

以数据字典的形式返回字段的定义，通过继承得来的字段也会在其中，string/help/selection属性会自动被翻译

**参数：**

- **fields** 参数是字段列表、为空或不传返回所有字段
- **attributes** 可指定字段的属性、为空或不传时返回全部的

### fields_view_get

返回指定视图的具体组成如：字段，模型，视图结构

**参数列表：**

- **view_id** 视图的id或None
- **view_type** 当view_id参数为空时指定视图类型如form,tree等
- **toolbar** 参数为true时将上下文动作包含在内odoo 视图

## 6、其他方法

### default_get

返回字段的缺省值

**参数：**

- **fields** ：字段列表

返回一个字典，包含字段及其默认值，若如果字段没有默认值，则返回值中没有该字段

### copy

复制当前的数据，并插入进数据库

**参数：**

- **default**：dict，默认使用当前self的数据，但是可以修改部分字段值以覆盖原数据

返回创建成功的数据对象

示例：

```python
newData = self.copy({'name': 'copy'})	#name字段使用copy，其他字段值不变
```

### name_get

返回 **self** 包含的数据集的数据，以list的形式，list的每一个元素代表一组数据(tuple)，但是只包括id和 **_rec_name** 指定的字段

示例：

```python
temp = self.name_get()
```

![1579059933632](1579059933632.png)

### name_create

根据传入的name（**_rec_name** 指定的字段）创建一条数据，其他字段使用默认值初始化

返回值是创建的数据的一组tuple，但是只包括id和 **_rec_name** 指定的字段

## 7、自动生成的字段

### id

字段id

### _log_access

是否应该生成日志访问字段（即 **create_date**、**write_uid** 等），默认True

### create_date

记录创建的日期，**Datetime** 类型

### create_uid

**res.users** 关联字段，记录创建者

### write_date

记录最近一次修改的时间，**Datetime** 类型

### write_uid

记录的最近一次的修改者，**res.users** 关联字段

## 8、保留字段

有一些字段名称的作用不仅仅是作为普通字段，而可以保留做一些预定义的行为，当需要的时候，可以在数据模型上定义

### name

**_rec_name** 的默认值，在某些场景下展示记录

### active

- 用作切换记录的全局可见性，Boolean类型；
- False表示该记录在大多数搜索和列表中不可见；

### sequence

- 可以更改的排序条件，Integer类型；
- 允许在列表视图中对数据记录重新排序；

### state

- 对象的生命周期循环，Selection类型；
- 用作 **states** 的字段属性

### parent_id

- Many2one类型，用于在树结构中对记录进行排序，并在域中是能 **child_of** 运算符；

### parent_left

用于 **_parent_store**，允许更快的访问树结构

### parent_right

和 **parent_left** 类似

# 四、创建数据模型

- 模型字段作为模型本身的属性定义；
- 字段的标签默认是字段名称的首字母大写，也可以用 **string** 来指定；

## （1）compute计算字段

类似vue的compute，在每次调用时计算得出

```python
people_count = fields.Integer(string=u'人数', compute='_get_people_count', store=True)

@api.multi
def _get_people_count(self):
    for model in self:
        model.people_count = len(model.name)
```

计算字段不会保存在数据库内，但是可以使用 **store** 参数，设为True，则可以保存在数据库内

## （2）onchange监测字段

实时更新UI

```python
@api.onchange('price', 'count')
def _onchange_price(self):
    self.totalPrice = self.count * self.price
```

- 当被监测的字段更改后，触发方法；
- 和 **depends** 不同的是，这里的触发不需要点击保存，界面实时修改，实时触发；

## （3）底层sql

env中的 **cr** 是数据库的光标，可以用它直接执行sql，一般用于ORM做不到的或者性能要求高的场景

```python
sql = "select * from table_name"
self.env.cr.execute(sql)
dicts = self.env.cr.dictfetchall()
```

- 因为 **env** 的方法会有一些缓存，当使用sql语句alter数据库的时候，必须要保证这些缓存失效，否则，模型以后的使用会出现无法意料的问题；
- 当使用 **create/update/delete** 等原生sql语句时，尽量先清除env的缓存，但是 **select** 除外；
- 可以使用 **env** 的 **invalidate_all()** 方法更新缓存，触发数据库操作；

# 五、新API和旧API的兼容

暂留

# 六、模型引用

**class odoo.models.Model(pool, cr)**

- odoo的数据库持续继承的主要超类；
- odoo 的数据模型都是继承此类；

```python
from odoo import models
class testModel(models.Model):
    pass
```

## （1）结构属性

**_name**

- 业务对象名称，若无继承，则必须；
- 使用点计法：**模块名.名称**；

**_rec_name**

- 用来指定odoo搜索和显示的字段，非必须；
- 如果不指定，则默认使用 **name** 字段；

**_inherit**

- 若 **_name** 已指定，则创建新表，且继承父模型的字段；
- 若 **_name** 没有指定，则拓展父模型，公用一张表；

**_order**

- 当搜索的时候作为排序字段；
- 如果没有指定，则默认使用 **id** 字段；

**_auto**

- 是否创建数据库表；
- 默认为True，即自动创建；
- 如果设置为False，则需要自己重载 **init()** 方法来创建表；

> 若要创建模型而不创建任何数据库表，则继承odoo.models.AbstractModel即可

**_table**

- 创建的数据库表名，默认使用 **_name** 属性（把 **.** 换成 **_**）；
- 若指定数据库名，其他模块仍然可以使用 **_name** 进行模块关联；
- 需要注意的是，指定的数据库表明不能使用点计法；

**_inherits**

- 子类通过关联字段和父类关联，子类不拥有父类的字段和方法，但是可以操作；

```python
_inherits = {
    '模型1_name': '本模型与之关联的字段',
    '模型2_name': '本模型与之关联的字段'
}
```

**_constraints**

字段约束，代码层面的约束

8.0版本之后弃用，使用 **constraints()** 替代，详情请见 **odoo.api.constrains(\*args)**

**_sql_constraints**

- 数据库约束，利用pg语句进行数据检查；
- 这是一个三元素的元组列表，三个元素依次是：sql约束名称、约束规则和违法警告；

```python
_sql_constraints = [
	('name_description_check',
	 'CHECK(name != description)',
	 '名称和描述不能相同'),
	('name_check',
	 'UNIQUE(name)',
	 '名称不能重复')
]
```

**_parent_store**

# 七、装饰器方法

## （1）odoo.api.multi

- 装饰一个记录集风格的方法，self是记录集；
- 装饰的方法通常是要操作记录集；

示例：

```python
@api.multi
def method(self, args):
    ...
```

## （2）odoo.api.model

装饰一个记录集风格的方法，self是记录集，但是这个记录集并不和数据相关，只是数据模型

和 **odoo.api.multi** 的区别在于:

​	**multi** 会带有当前的记录集，而 **model** 只有当前模型的对象没有记录集，所以，**model** 一般用于不会操作记录集的方法

示例：

```python
@api.model
def method(self, args):
    ...
```

## （3）odoo.api.depends

- 依赖字段
- **@api.depends** 指定依赖，依赖被修改，则重新触发计算字段更新，可以同时传多个依赖字段。使用依赖，则修改数据；
- 和 **onchange** 不同的是，点击保存后，才会触发；

```python
@api.multi
@api.depends('value', 'name')
def _get_people_count(self):
    for model in self:
        model.people_count = len(model.value)
```

## （4）odoo.api.constrains

模型约束，点击保存，保存数据库之前，会触发方法，检查特定字段

```python
@api.constrains('count')
def _check_count(self):
    for product in self:
        if product.count < 0:
            raise ValidationError('Input count cannot less 0 :%s' % product.count)
```

## （5）odoo.api.onchange

- 当被监测的字段更改后，触发方法；
- 和 **depends** 不同的是，这里的触发不需要点击保存，界面实时修改，实时触发；

```python
@api.onchange('price', 'count')
def _onchange_price(self):
    self.totalPrice = self.count * self.price
```

## （6）odoo.api.returns

- 返回一个方法装饰器，能够获取数据模型的实例；
- 主要是用来指定返回值的格式，可以接收三个参数：
  - 返回值模型，如果是对象本身，则为self；
  - 向下兼容的方法；
  - 向上兼容的方法；
- 主要用在确保新旧API的返回值的一致，并不常用；

## （7）odoo.api.one

- 9.0开始已经弃用；
- 装饰一个记录集风格的方法，self是记录集，但是只有一条记录；

## （8）odoo.api.v7/v8

- 装饰只能用于新风格的API的方法；
- 通过重新定义相同函数名称并使用装饰器修饰的方法，提供旧风格的API；

# 八、字段fields

```python
class odoo.fields.Field(string=<object object at 0x68aa320>, **kwargs)
```

**常用的字段类型：**

| 字段类型  | 说明     | 备注                                                         |
| --------- | -------- | ------------------------------------------------------------ |
| Char      | 字符型   | size限制字符长度                                             |
| Boolean   | 布尔型   |                                                              |
| Float     | 浮点型   | digits=(9,3) 限制大小和精度这里表示数字总长度9，小数部分3，不足补0 |
| Integer   | 整形     |                                                              |
| Date      | 日期     | 年月日                                                       |
| Datetime  | 时间戳   | 可以使用fields.Date.today获取当前日期                        |
| Text      | 文本     | 可用于多行文本                                               |
| Html      | 文本     | 类似Text，自带html编辑器，自定解析                           |
| Selection | 下拉列表 | 枚举类型                                                     |
| Monetary  | 货币数   | 类似浮点数，带有货币特殊处理                                 |
| Binary    | 二进制   | 可有python的base64编码字符串处理                             |

## 1、基础字段

### （1）odoo.fields.Field

- 描述一个字段，并管理记录集上该字段；

**字段的参数：**

| 参数     | 描述                                                         |
| -------- | ------------------------------------------------------------ |
| string   | 客户可见的字段标签，默认使用该字段的类名                     |
| help     | 用户可见的字段的tooltip                                      |
| readonly | 该字段是否只读，默认False                                    |
| required | 该字段是否是必须的，默认False                                |
| index    | 该字段是否加入数据库索引，默认False                          |
| default  | 该字段的默认值，可以是一个值，也可以是一个可以返回记录的方法<br>default=None则舍弃字段的默认值 |
| states   | 一个字典列表，包含UI的属性键值对，包含的属性包括：readonly、 required、invisible |
| groups   | 一个字符串列表，包含权限xml中的group的id，包含了指定组的访问权限 |
| copy     | 记录复制时，是否copy该字段<br>普通字段默认True，关联字段和计算字段默认False |
| oldname  | 该字段的以前的名称，可以使用ORM找到对应的字段进行数据更新    |

#### a. 计算字段

- 定义字段的时候，添加属性 **compute**（值为方法名字符串），则表示该字段是一个计算字段；
- 计算字段的值不会保存在数据库中，而是通过指定的方法计算得出；
- 每次需要用到计算字段的时候，都会执行一遍计算方法；

**计算字段的参数：**

| 参数         | 说明                                                    |
| ------------ | ------------------------------------------------------- |
| compute      | 计算字段指定的计算方法名                                |
| inverse      | 可选，逆向计算方法                                      |
| search       | 可选，传入方法名，当按照该字段进行搜索时，先触发该方法  |
| store        | 该字段是否要保存在数据库中，默认False                   |
| compute_sudo | 当超级用户绕过访问权限时，是否重新计算该字段，默认False |

1. 需要使用 **odoo.api.depends()** 必须用在计算方法上，并制定计算依赖字段。这样，在依赖字段变更时，会重新计算；
2. **inverse** 参数用于逆向计算，若没有指定 **inverse** ，则计算字段是readonly的。指定inverse之后，可以在前端修改计算字段；
3. **search** 用于在搜索之前执行方法，且方法必须返回和条件等价的domain，即 **字段 操作符 数值**；

**inverse** 示例：

```python
value = fields.Integer(string=u'数值', default=10)
count = fields.Integer(string=u'数量', default=2)
price = fields.Integer(string=u'价格', compute='_calc_price', inverse='_inverse_price')

@api.multi
@api.depends('value', 'count')
def _calc_price(self):
	for data in self:
		data.price = data.value * data.count;

@api.multi
def _inverse_price(self):
	for data in self:
		data.value = data.price / data.count;
```

**search** 示例：

下面示例中，如果根据字段price进行搜索，则最后搜索条件会变成根据字段name包含aaa的条件进行搜索

```python
price = fields.Integer(string=u'价格', search='_search_price')
@api.multi
def _search_price(self, operator, value):
	return [('name', 'ilike', 'aaa')]
```

#### b. 关联字段

表示本字段引用关联表中的某字段。

fields.related(关系字段,引用字段,type,relation,string,...)

关系字段是本对象的某字段（通常是 **one2many**或者 **many2many**）

引用字段是通过关系字段关联的数据表的字段，type是引用字段的类型，如果type是many2one or many2many，relation指明关联表。

示例：

```python
address = fields.one2many('res.partner.address','partner_id','Contacts'),
city = fields.related('address','city',type='char',string='City'),
country = fields.related('address','country_id',type='many2one',
                         relation='res.country',string='Country'),
```

**说明：**

- city引用address的city字段，country引用address的country对象。
- 在address的关联对象res.partner.address中，country_id是many2one类型的字段，所以 **type='many2one',relation='res.country'**。
- address是一个one2many说明它是一个res.partner.address对象，city就依赖address对象的city字段，

去res.partner.address中查看city是char类型，所以city type=‘char’

- country依赖address的country_id字段，同样的方式，去res.partner.address中查看是many2one类型，对应'res.country'，所以也是many2one

注意：

 related使用时候，需要在相应的py文件字段上加readonly属性 

#### c. Company-依赖字段

就是以前的 **property** 字段，这类字段的值是依赖于 **company**，也就是说，归属于不同公司的user对某一个record的field，得到的值是不同的。 

**参数：**

**company_dependent**：布尔型，该字段是否是company依赖字段

#### d. 稀疏字段

#### e. 增量定义

字段是被定义为模型类上的属性，如果模型被扩展了，可以通过在子类上重新定义相同名称和类型的字段来扩展字段的定义。

字段的属性来自父类，随后，使用子类的冲定义覆盖。

示例：

```python
class First(models.Model):
    _name = 'foo'
    state = fields.Selection([...], required=True)

class Second(models.Model):
    _inherit = 'foo'
    state = fields.Selection(help="Blah blah blah")
```

### （2）odoo.fields.Char

继承自：**odoo.fields._String**，单行字符串

**特有参数：**

​	**size**：整形，数值的最大长度

​	**translate**：设置内容是否可以用于被翻译

### （3）odoo.fields.Float

**特有参数：**

​	**digits** ：带有两个元素的元组，第一个元素表示总长度，第二个元素表示小数位长度

### （4）odoo.fields.Text

和 **odoo.fields.Char** 类型，用于显示多行文本，不限定长度

**特有参数：**

​	**translate**：设置内容是否可以用于被翻译

### （5）odoo.fields.Selection

可供选择的字段

**特有参数：**

​	**selection**：元组列表，表示可供选择的内容

​	**selection_add**：元组列表，在覆盖的情况下，提供可供选择的内容的扩展

### （6）odoo.fields.Date

日期类型，常用方法如下：

**context_today(*record, timestamp=None*)**：

​	返回客户端所在的时区的当前日期

**now(*args)**：

​	以ORM的数据类型返回当前时间，一般用于计算默认值

**from_string(value)**：

​	将ORM的value转换成datatime值

**to_string(value)**：

​	和 **from_string** 相反

### （7）odoo.fields.Datetime

日期时间类型，常用方法和 **odoo.fields.Date** 相同

## 2、关联字段

### （1）多对一

**odoo.fields.Many2one**

**参数：**

| 参数         | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| comodel_name | 多对一的目标模型                                             |
| domain       | 可选，用于过滤目标模型的数据                                 |
| context      | 可选，字典类型，当处理该字段时客户端使用的上下文             |
| ondelete     | 当关联的字段数据被删除后的操作，可能的值是：**'set null'、'restrict' 和 'cascade'** |
| auto_join    | 布尔型，是否通过该字段的搜索生成连接，默认False              |
| delegate     | 布尔型，True则表示关联的模型字段可以在当前模型访问           |

### （2）一对多

**odoo.fields.One2many**

| 参数         | 说明                                             |
| ------------ | ------------------------------------------------ |
| comodel_name | 一对多的目标模型名                               |
| inverse_name | 目标模型中表示当前模型的字段                     |
| domain       | 可选，用于过滤目标模型的数据                     |
| context      | 可选，字典类型，当处理该字段时客户端使用的上下文 |
| auto_join    | 布尔型，是否通过该字段的搜索生成连接，默认False  |
| limie        | 可选，读取的时候限制数量                         |

### （3）多对多

**odoo.fields.Many2many**

| 参数         | 说明                                             |
| ------------ | ------------------------------------------------ |
| comodel_name | 多对多的目标字段                                 |
| relation     | 可选，保存关联管理的数据表名                     |
| column1      | 可选，保存关联关系的目标数据的字段名             |
| column2      | 可选，保存关联关系的当前数据的字段名             |
| domain       | 可选，用于过滤目标模型的数据                     |
| context      | 可选，字典类型，当处理该字段时客户端使用的上下文 |
| limit        | 可选，读取的时候限制数量                         |

### （4）引用

**odoo.fields.Reference**

继承自 **odoo.fields.Selection**

# 九、继承和扩展

odoo数据模型的继承有三种方式：

- 原型继承：根据现有的模型创建新模型，保留原模型的字段，在新模型中添加新的信息，会在数据库中添加新表；
- 扩展继承：在现有的模型上拓展，替换原模型，会在原数据表中拓展，不会新增表；
- 委托继承：将一些模型字段委托给它包含的记录？？

## 1、原型继承

使用 **_inherit** 指定继承模型，**_name** 指定新模型的名称

新模型会从继承的模型中继承所有的字段、方法和元信息。也可以定义相同名称的方法或者字段以达到覆盖效果。

## 2、扩展继承

使用 **_inherit** 指定拓展的模型，但是不提供 **_name**

新模型取代原模型，并在原模型上扩展，可以覆盖旧字段和方法，也可以添加新字段和方法

## 3、委托继承

委托继承并不常用，但是却提供了一个很好的解决问题的方法。

委托继承使用 **_inherits** 属性（注意这有 **s**），通过字典类型，确定继承关系，并关联数据，实际上，新模型是嵌入在父模型中的，虽然没有定义字段，但是能使用父模型的字段数据，而且创建了新的数据表，但是数据表中只有扩展字段

委托继承只能继承字段，不能继承方法

**示例：**

**type.py**

```python
class TypeModule(models.Model):
    _name = 'test.type'

    name = fields.Char(string=u'名称', size=50, required=True)
    value = fields.Integer(string=u'数值')
```

**object.py**

```python
class ObjectModule(models.Model):
    _name = 'test.object'

    object_name = fields.Char(string=u'名称', size=50, required=True)
    object_value = fields.Integer(string=u'参数')
```

**product.py**

```python
class NewTest(models.Model):
    _name = 'test.product'
    _inherits = {
        'test.type':'type_id', 
        'test.object':'object_id'}

    type_id = fields.Many2one('test.type')
    object_id = fields.Many2one('test.object')
    unit_cost = fields.Float(u'单价')
```
**\_\_init\_\_.py**:

```python
import type
import object
import product
```

**说明：**

- 委托继承支持多继承，使用字典的形式；
- product继承type和object，并使用 **Many2one** 字段关联，product模型可以使用type和object的所有字段；
- unit_cost为product的扩展字段，会出现在product的数据表中；
- 在前端创建一个product数据，则会相对应创建type和object数据，但是要符合字段条件，例如 **required** 等；
- 若多继承中出现相同的字段，则以先出现的同名字段为准，即若type和object都有字段name，则product的name字段以type为准；
- py文件的引入要注意顺序，若果object或者type在product后面引入，则会出错；


# 十、domain

- domain一般用于方法的过滤；
- 是一个列表，每一个元素都是一个三元素元组，三个元素分别是（字段名，操作符，条件值）；
- 每个元组中间需要用与或非操作符连接，不写，则默认 **&**；

**字段名：**

​	需要过滤的模型字段名

**条件值：**

​	用于判断的条件数值

**操作符：**

​	用于字段值和条件值比较的操作符，可以是：

|  操作符   | 说明                                                         |
| :-------: | ------------------------------------------------------------ |
|     =     | 相等                                                         |
|    !=     | 不等                                                         |
|     >     | 大于                                                         |
|    >=     | 大于或等于                                                   |
|     <     | 小于                                                         |
|    <=     | 小于或等于                                                   |
|    =?     | 未设置或等于<br>若条件值为 **None** 或者 **False**，则返回true，否则同= |
|   =like   | 相似匹配，**%** 匹配任意字符串，**_** 匹配单个字符，区分大小写 |
|   like    | 和 **=like** 类似，但匹配之前会用 **%** 包装条件值，区分大小写 |
| not like  | 和 **like** 相反，，区分大小写                               |
|   ilike   | 等同于不区分大小写的 **like**                                |
| not ilike | 等同于不区分大小写的 **not like**                            |
|  =ilike   | 等同于不区分大小写的 **=like**                               |
|    in     | 条件值应该是一个列表，判断字段值是否在列表中                 |
|  not in   | 和 **in** 相反                                               |
| child_of  | 判断是否为直接或者间接的子对象                               |

**child_of**

```python
[('create_uid.company_id','child_of',[user.company_id.id])]
```

等价于：

```python
['|',('create_uid.company_id','=',[user.company_id.id]),('create_uid.company_id.parent_id','=',[user.company_id.id])]
```

## domain的条件组合

**'&'**：并，组合条件的默认操作，没写则默认 **&**

**'|'**：或

**'!'**：非

示例：

```python
[('name','=','ABC'), 
 ('language.code','!=','en_US'),
 '|',('country_id.code','=','be'), ('country_id.code','=','de')]
```

等同于：

```python
(name is 'ABC')
AND (language is NOT english)
AND (country is Belgium OR Germany)
```



​	