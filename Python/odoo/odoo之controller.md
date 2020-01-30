<font size=3>

# 说明

odoo 的路由需要引入http模块

controllers能够像model一样拓展，只需要继承 **http.Controller** 即可。

而内部定义的方法，需要添加 **route()** 装饰器才能起到路由的效果

示例：

```python
from odoo import http
class MyRoute(http.Controller):
    @http.route(['/type'], type='http', auth='public', methods=['GET'])
    def hander(self, **kwargs):
        return 'hello world'
```

若需要覆盖controllers，则需要继承，并重写方法，并重新暴露接口即可

```python
from odoo import http
class RouteExtension(MyRoute):
    @http.route(methods=['GET', 'POST'])
    def hander(self, **kwargs):
        # doSomething
        return super(RouteExtension, self).hander()
```

上例中，需要使用 **route()** 装饰器，但是参数可以不必写全，没写的参数默认从父类继承，需要修改的参数则重新定义覆盖

# Routing

controller中使用装饰器来标记请求的处理程序

```python
odoo.http.route(route=None, **kw)
```

**参数：**

**route：** 

​	字符串/数组，表示处理请求的url，若是数组，则支出多个不同的url请求。

​	且url必须以正斜杠开头，否则会报错 ***urls must start with a leading slash***

**type：** 

​	请求的类型，**http** 或者 **json**，表示请求的参数传入方式

​	json表示请求的参数需要以json数据对象的方式传入，需要调用json rpc方法

​	http表示请求传递的参数以字符串的方式，而不是json

**auth：**

​	身份验证的方法类型，可以是 **user、public、none**

​	user表示必须对用户进行身份验证，并且当前的请求会按照该用户的权限进行处理。即若没有登录，则会跳转到登录页面

​	public表示用户身份可验证也可不验证。若不验证，则使用共享的公共用户执行，即可以不登录验证直接访问

​	none一般用于框架和认证模块，对应请求没有办法访问数据库或指向数据库的设置

**methods：**

​	指定请求的方法，是一个列表，可以支持多种方法，若没有指定，则默认所有方法都支持

​	方法包括：GET、POST、PUT、PATCH、DELETE

**cors：**

​	表示跨域指令值

**csrf：**

​	表示是否为该路由开启CSRF保护，默认开启

1. 如果表单是用python代码生成的，可通过request.csrf_token() 获取csrf
2. 如果表单是用javascript生成的，CSRF token会自动被添加到QWEB环境变量中，通过require('web.core').csrf_token获取
3. 如果终端可从其他地方以api或webhook形式调用，需要将对应的csrf禁用，此时最好用其他方式进行验证

示例：

```python
@http.route(['/web/type', '/type'], type='http', 
            auth='public', methods=['POST', 'GET'])
def getEquipByObjectTypeHttp(self):
	return "12345"
```

# Request

在请求开始的时候，请求的所有数据都会被设置到 **odoo.http.request** 中，包含的内容包括参数、cookie、session对象等数据

在使用时，通过调用 **odoo.http.request** 来获取请求对象，并进行一系列操作即可

请求相关的类和函数有：

## 1. odoo.http.WebRequest(*httprequest*)

所有odoo的web请求的父类，一般用于 **请求对象的初始化**，而不进行具体的数据处理，数据处理使用其具体子类进行

所携带的参数包括：

**httprequest**

​	提供请求的原始对象

**params**

​	请求的传入参数，不常使用，一般请求的参数会通过方法的关键字参数直接传入

**cr**

​	当前方法使用的初始游标，当使用none的认证的时候，读取游标会出错

示例：

```python
cr = http.request.cr
cr.execute(sql)	#执行sql语句
dicts = http.request.cr.dictfetchall()	#获取sql 执行结果
```

**context**

​	请求的上下文键值

**env**

​	绑定到当前请求的odoo的 **Environment** 对象，可以使用其进行 **env()** 方法的调用

示例：

```python
result = http.request.env['pam.equip'].search([('id', '=', '1')])
```

**session**

​	提供当前http的session数据的 **OpenERPSession** 对象

**debug**

​	表示当前的请求是否处于debug模式，即开发者模式

​	若激活/关闭开发者模式，则为 **True/False**

​	若激活开发者模式(assets)，则为 **assets**

​	若请求不是来自odoo，则为 **NoneType**

**db**

​	与请求连接的数据库名，若当前请求使用none认证时，值为None

**csef_token()**

  根据当前的session数据，返回一个CSRF token

## 2.odoo.htpp.HttpRequest(*\*args*)

**odoo.http.WebRequest** 子类，用于处理type为http的请求

匹配的路由参数、查询字符串参数、表单数据和文件作为关键字阐述传入

若出现名称冲突，路由参数具有最高优先级

该处理方法的结果可能是：

- 无效值，此时HTTP的响应返回204；
- 一个werkzeug响应对象；
- 一个str/unicode，包含在Response对象中，并使用HTML解析

**make_response(*data, headers=None, cookies=None*)**

辅助生成自定义的响应headers或者cookies，可以带HTML响应，也可以不带

处理方法只能以字符串的形式返回HTML页面的标记，如果返回HTML的数据，则需要创建一个简单的reponse对象，否则，返回的数据无法被客户端正确地解析

**not_found(description=None)**

给出404 Not Found的响应，可以自定义描述

**render(*template, qcontext=None, lazy=True, \*kw*)**

渲染QWeb模板，调度完成后，对指定的模板进行渲染，返回给客户端

​	template：字符串，需要渲染的模板的id

​	qcontext：字典，渲染内容使用

​	lazy：bool，渲染动作是否延迟到最后执行

## 3.odoo.http.JsonRequest(*\*args*)

**odoo.http.WebRequest** 子类，用于处理type为json的请求

- **method** 忽略
- **params**：必须是json对象，作为关键字参数传递给处理方法
- 处理方法返回的结果是一个 JSON-RPC格式，包在JSON-RPC Response对象中

# Response

```python
odoo.http.Response(*args, **kw)
```

可以通过controller传递的响应对象，除了 **werkzeug.wrappers.Response** 参数之外，该类还可以添加用于QWeb的渲染参数，包括：

​	template：字符串，qweb模板id

​	qcontext：字典，需要的渲染上下文

​	uid：int，用于 **ir.ui.view** 渲染调用的用户id，默认None（表示请求的用户）

这些参数可以在渲染之前修改

**render()**

​	渲染响应模板，返回结果

**flattern()**

​	强制渲染响应模板，将结果作为响应主体，并重设模板

