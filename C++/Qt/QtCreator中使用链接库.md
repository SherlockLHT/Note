[TOC]

## 说明

之前讨论的DLL的静态链接和动态连接都是基于 **MSVC** 编译器，但是 **MingW** 似乎有另外一套类似但是不相同的机制。下文均在 **windows** 下使用 **Qt Creator** 中使用 **MingW** 进行说明。

我们在新建库项目的时候有三种选项，如图所示：

![1557975168306](E:\MyNote\Note\C++\Qt\1557975168306.png)

![1557975205631](E:\MyNote\Note\C++\Qt\1557975205631.png)

三种类型分别是：共享库、静态链接库和Qt插件，之间区别以及和 **MSVC** 的库区别如下：

1. 项目会根据类型不同生成 .dll 和 .a文件，这里的 .a 即类似 .lib，但是又不完全相同；
2. **共享库** 类似 msvc 的静态链接，构建最终生成 .a(类似.lib) 和 .dll，客户端编译时需要头文件和 .a，运行时需要 .dll；
3. **静态链接库** 则是另外一种新的机制，构建最终只生成 .a 文件，客户端调用时需要 .a 文件，运行时则不需要任何库文件，类似于客户端在编译时将库包进了自己的exe中；
4. Qt插件不再多少，参考Qt插件系统文章，就是一种动态加载DLL的方法，但是又把加载的细节隐藏了；

综上所属，MingW 下的链接库相比于 MSVC，着实简单了很多，把内部的很多复杂的细节隐藏在Qt的内部系统中，对使用者来说，更加方便。

## 链接库的使用

下面就来简单介绍一下这些库的使用，Qt的插件不再讨论之列，请参考Qt插件系统文章。介绍中会忽略一些不重要的细节，我们认为你应该对C++和Qt有一个较深的认识，若没有，请自行去学习相关知识。同时，我们顺带简单介绍一些 .pro 文件的配置。

### 共享库

#### （1）创建共享库

共享库类似静态链接，生成.a文件和.dll文件。

按照正常流程新建库项目，类型选择 **动态库**，最终生成的项目列表中如下如所示：

![1557976393785](E:\MyNote\Note\C++\Qt\1557976393785.png)

可以看出，除了需要导出的类外，额外还有一个XXX_global.h的文件，文件代码如下：

```c++
#ifndef FIRECONTROL_GLOBAL_H
#define FIRECONTROL_GLOBAL_H

#include <QtCore/qglobal.h>

#if defined(FIRECONTROL_LIBRARY)
#  define FIRECONTROLSHARED_EXPORT Q_DECL_EXPORT
#else
#  define FIRECONTROLSHARED_EXPORT Q_DECL_IMPORT
#endif

#endif // FIRECONTROL_GLOBAL_H
```

了解 MSVC 的dll导出的都能了解，这里其实是对 **__declspec(dllexport)** 和 **__declspec(dllimport)** 进行一种巧妙的声明，因为导出需要使用export，导入需要使用import，为了避免在库和客户端中重复地进行export和import的声明，这里使用宏定义进行统一声明，详细说明可以参考DLL的导出和调用的文章。

在需要导出的类和方法前面使用 **FIRECONTROLSHARED_EXPORT** 声明即可，客户端调用时也需要将这个文件include进来。

当然，如果你对导出DLL的一个比较清晰的认识，也可以删除这个文件，自己定义。

#### （2）pro文件

```python
QT       -= gui

TARGET = FireControl
TEMPLATE = lib	#表示这是一个库项目

DEFINES += FIRECONTROL_LIBRARY

SOURCES += FireControlManager.cpp

HEADERS += FireControlManager.h\
        firecontrol_global.h

unix {
    target.path = /usr/lib
    INSTALLS += target
}
#上面为创建时自动生成，以下为新增
DESTDIR = $$PWD/../SystemWindow/lib				#最终编译文件的生成路径，构建后会有.a和.dll
DLLDESTDIR = $$PWD/../../../ATM6000V2.1_Service	#dll文件的生成路径，构建后会有.dll
```

#### （3）调用共享库

在调用端右击->添加库，选择生成 .a 文件，即可自动在 pro文件中添加加载设置。当然，也可以不适用GUI操作，直接修改pro文件以达到添加库的目的。

![1557977308371](E:\MyNote\Note\C++\Qt\1557977308371.png)

我们希望调用端和库项目分开构建，所以这里选择外部库

![1557977701438](E:\MyNote\Note\C++\Qt\1557977701438.png)

添加.a的库文件和头文件的包含目录，并配置一些链接方法

![1557977791495](E:\MyNote\Note\C++\Qt\1557977791495.png)

调用设置完成后，pro文件如下：

```python
QT       += core gui

greaterThan(QT_MAJOR_VERSION, 4): QT += widgets

TARGET = SystemWindow
TEMPLATE = app

SOURCES += main.cpp\
        MainBench.cpp

HEADERS  += MainBench.h

FORMS    += MainBench.ui

#以下即为自动生成的加载库设置
win32:CONFIG(release, debug|release): LIBS += -L$$PWD/lib/ -lFireControl
else:win32:CONFIG(debug, debug|release): LIBS += -L$$PWD/lib/ -lFireControl
else:unix:!macx: LIBS += -L$$PWD/lib/ -lFireControl

INCLUDEPATH += $$PWD/../FireControl
DEPENDPATH += $$PWD/../FireControl
DESTDIR = $$PWD/../../../Service	#目标生成路径
```

最后，可以在客户端使用库导出的类了，当然不要忘了添加头文件。

### 静态库

#### （1）创建静态库

静态最终只会生成 .a 文件。

创建库项目时，选择静态库，完成之后，项目目录的内容和目录中，除了没有入口的main函数外，其他都和普通项目没有什么区别，而真正的区别在于pro文件的配置中。

#### （2）pro文件

```python
QT       -= gui

TARGET = untitled
TEMPLATE = lib		#表示这是一个库项目
CONFIG += staticlib	#表示这是一个静态库

SOURCES += untitled.cpp

HEADERS += untitled.h
unix {
    target.path = /usr/lib
    INSTALLS += target
}
DESTDIR = $$PWD/../SystemWindow/lib #生成路径
```

#### （3）调用静态库

调用方法和调用共享库类似，这里不做赘述，最终实际运行时不需要dll文件

## 总结

共享库和静态库各有各的优势，从最简单的层面上来看：

使用静态库可以将大项目区分开，即分模块显示项目，便于维护；

使用共项目也是如此，而且可以在不修改客户端的情况下，修改库内容的实现，只要接口不变，最终替换dll即可完成内容的改变。

但是看起来静态库有些鸡肋，具体还有哪些好处，留待日后发现。