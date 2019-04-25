原文：https://blog.csdn.net/hl1hl/article/details/85244451 

## 前言
在Qt开发桌面软件的过程中，根据开发的需求不同，我们经常需要将弹出窗口，一般常见的是需要是以下两种。

锁定弹出的窗口，阻塞其他窗口（包括主窗口）的操作，只有关闭当前窗口才能点击其他窗口或者主窗口
保持当前窗口一直显示在最顶层，但是不锁定（即同时可以操作其他窗口），同时也需要保证不影响其他程序

## 知识准备
首先我们需要了解一下 **QMainWindow、QWidget、QDialog** 的区别。

具体可以看以下链接，我只说下结论。

- 如果需要嵌入至其他窗体中，则基于 **QWidget** 创建；

- 如果是顶级对话框，则基于 **QDialog** 创建；

  - **QDialog** 又分为非模态对话框、模态对话框、半模态对话框

- 如果是主窗体，则基于 **QMainWindow** 创建

  相信你看了上面的许多开发者就会知道自己的一个最基本的错误的：通过继承 **QWidget** 来创建弹出窗口，这个最基本的错误会导致你无法实现以上两点将窗口置顶以及其他不合理的Bug。

记住重要的一点，创建顶级（弹出）对话框，基于 **QDialog** 来创建。如果是弹出窗口基于 **QWidget** 来创建也没有关系，不需要重要修改太多，只需 **setWindowflags(Qt::Dialog)**。

## 具体操作
### 一、针对第一种锁定弹出窗口

#### 1、如果窗口是基于QDialog创建。

```c++
topWindow.setParent(this);//指定父窗口，一般是目前将你弹出的窗口
topWindow.exec();//模态
```

 ####  2、如果窗口是基于QWidget创建（不建议这么做）

```c++
topWindow->setWindowFlags(topWindow->windowFlags() |Qt::Dialog);
topWindow->setWindowModality(Qt::ApplicationModal); //阻塞除当前窗体之外的所有的窗体
topWindow->show();
```

### 二、设置窗口一直保持在顶层，但是不阻塞用户操作其他窗口

#### 1、如果窗口是基于QDialog创建的

```c++
topWindow=new TopWindow(this);//指定父窗口
topWindow.show();//非模态
```

#### 2、如果窗口是基于QWidget创建的(不建议)

```c++
topWindow.setParent(this);//指定父窗口
topWindow.setWindowFlags(topWindow.windowflags()| Qt::Dialog);
topWindow.show();
```


以上 **topWindow** 是窗体类新建的对象。

## 其他的窗口置顶方式

其实有其他将窗口置顶的方式，但是是对于所有程序的窗口都置顶。也就是说其他程序打开后也在被置顶的窗口所遮盖。

我所知道的有两种方式：

```c++
topWindow->setWindowFlags(topWindow->windowFlags() | Qt::WindowStaysOnTopHint);
```

或者

```c++
SetWindowPos(HWND(topWindow->winid()),HWND_TOPMOST,0,0,0,0,SWP_NOMOVE|SWP_NOSIZE);
```