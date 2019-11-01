# 说明

- odoo内的所有应用都是以module的形式构成的

## 1 创建module

可以使用指令，创建module

```cmake
odoo-bin scaffold test_module addons
```

- 以上指令表示，在 **addins** 目录下创建新module，名称是 **test_module**；
- 建议使用指令创建module，因为不同的odoo版本的某些文件的内容格式有所出入，手动添加可能出错；

## 2 module的组成

- **controllers：**存放网络浏览器的请求的接口，.py文件
- **demo：**存放数据文件，在安装模块的时候，demo里的数据将会添加到数据库中，.xml文件
- **i18n：**存放翻译文件，在国际化一章中会讲解
- **models：**存放数据模型文件，python文件，用于描述业务对象，一般是 **.py** 文件
- **security：**存放权限文件，用于用户权限控制，有.csv、.xml等
- **views：**存放视图文件，一般是.xml，描述前端显示的内容

上面几个模块中，如果里面存放python文件，则目录内也会包含一个**\_\_init\_\_.py**文件，用处是夹在python模块

**\_\_init\_\_.py：** 

	- module的根目录下，作用是加载上面几个目录中加载python模块
	- 在上面的文件夹中添加python模块后，需要在此文件中import，否则不生效

**__manifest__.py：**

- 详细说明参考文档：https://www.odoo.com/documentation/10.0/reference/module.html

	- 内容是一个python字典
	- 描述module的一些属性，并指定上面目录中除python文件外的其他文件的用途
