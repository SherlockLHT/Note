[TOC]

## 说明

近期入职新公司，新公司的项目用到了Qt的插件系统，花时间了解了一下，还以为Qt的插件系统有多么高级呢，原来归根到底还是 **dll** 的动态调用时获取其中的类那一招啊，原理和之前的文章**《DLL的动态加载》** 的里使用 **使用dll中的类** 描述的方法如出一辙，只是Qt利用了其库的优势。

#### 动态加载dll获取类

在 **《DLL的动态加载》** 文章说已经说明，dll只可以导出函数，不可以导出指针，但是为了能得到dll中的类，可以导出一个接口，使用接口获取对象指针。但是在dll的调用一方，却无法识别获取到的类指针，所以利用C++的多态和继承的特性来完成。使用抽象类，声明好抽象接口，将需要导出的类继承自该抽象类，并实现其声明的接口。dll的导出方和调用方公用一个抽象类声明文件，在dll导出函数内部，利用多态，将抽象的类指针new成子类对象，并返回出来，则调用方就可以识别得到的类指针了。具体方法说明请参考文章**《DLL的动态加载》** 。

### Qt的插件系统

Qt的插件系统原理（这里只讨论High-Level API）与此相同，只是Qt利用了其内部非特性，使用QPluginLoader替代了导出类指针的接口，并使用宏定义进行导入和导出，避免了在导出导出上因为为了兼容C语言而导致的一些问题，并简化了一些操作。

## 使用方法

既然原理相同，则需要的文件也相同，需要导出的dll文件和抽象类的声明文件。不同点是，不需要在使用C风格的函数导出类指针了，Qt里面使用宏定义替代。

**步骤：**

1. 定义抽象类，并声明需要用到的接口；
2. 使用 **Q_DECLARE_INTERFACE()** 宏定义将这个抽象类的信息告诉Qt的元对象系统；
3. 建立dll项目，继承自 **QObject** 和这个抽象类，并实现其接口，我们称这个项目为插件；
4. 在插件的头文件中使用 **Q_PLUGIN_METADATA()** 宏定义导出这个插件；
5. 在插件项目的.pro文件中配置导出设置，编译生成dll；
6. 在dll调用程序中加载dll；

#### 示例代码：

#####　插件项目plugin：

```js
HEADERS += \
    plugin.h

SOURCES += \
    plugin.cpp

QT += widgets

DISTFILES += \
    plugin.json

CONFIG += plugin    #表示用于lib模板：库是一个插件
TEMPLATE = lib      #表示生成库文件
```

```c++
//plugin.h
class Plugin : public QObject, public PluginInterface
{
    Q_OBJECT
    Q_PLUGIN_METADATA(IID pluginInterface_iid FILE "plugin.json")
    Q_INTERFACES(PluginInterface)
public:
    explicit Plugin(QObject *parent = 0);
    ~Plugin();
    void SayHello(QWidget* parent) Q_DECL_OVERRIDE;
};
```
**说明：**

- 必须多继承自 **QObject** 和抽象类；
- **Q_PLUGIN_METADATA**  描述插件元数据，**IID** 是必须且唯一的，且应该和插件标识符相同，**FILE** 是可选的，后面跟着一个json文件，用于描述插件的相关数据信息；
- **Q_INTERFACES** 的作用是将所实现的插件接口通知给元类型系统，参数是抽象类类名；
- **Q_DECL_OVERRIDE** 用于表示这是一个虚函数，编译器会检查这个方法是不是父类中所有的，若没有则报错(类似java中的@Override注解)；

```c++
//plugin.cpp
Plugin::Plugin(QObject *parent) : QObject(parent)
{
    qDebug()<<"constructor";
}

Plugin::~Plugin()
{
    qDebug()<<"destructor";
}

void Plugin::SayHello(QWidget *parent)
{
    QMessageBox::information(parent, "plugin", "hello, this is dynamically loaded.");
}
```

```json
{
    "Keys" : [ "plugin" ]
}
```

##### 调用项目test：

插件生成的dll文件copy到执行目录下，共调用方加载

````c++
//interface.h
class PluginInterface
{
public:
    virtual ~PluginInterface(){}
    virtual void SayHello(QWidget* parent) = 0;
};

#define pluginInterface_iid "io.qt.dynamicplugin"
Q_DECLARE_INTERFACE(PluginInterface, pluginInterface_iid)
````
**说明：**

- **Q_DECLARE_INTERFACE** 宏告诉Qt，这是一个插件接口，第一个参数是接口类名，第二个参数是插件标识符，大小写敏感，且唯一；

```c++
//调用部分
PluginInterface* interface = NULL;
QPluginLoader plugin_loader("plugin.dll");
QObject* plugin = plugin_loader.instance();
if(plugin)
{
	interface = qobject_cast<PluginInterface*>(plugin);
	if(interface)
    {
		interface->SayHello(this);
	}
	//delete plugin;
	plugin_loader.unload();
}
```

注：Qt文档中说明，**QPluginLoader** 释放之后，其获取到的插件不会释放，所以，需要手动 **delete** 插件，或者使用 **unload()** 方法释放。

