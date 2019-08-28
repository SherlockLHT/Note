# 前言

很久以前就想过要阅读一些高质量的源代码，但是直到最近才真正开始。

而这次从Qt的线程模块开始阅读，也有一部分原因是，项目中遇到了难以解决的问题，不得不从源码上找原因。

这世上很多事情都是这样，你早就想做，但是却因为种种原因被耽搁，而真正做了却是因为不得不做，最后，明明是想主动的事，却成了被动完成。

其实，严格意义上说，这次阅读源码并不是第一次，两年前在那家只待了两个月的公司，第一次阅读了Qt Creator的源码，虽然只是一小部分，但也是那一小部分，改变了我的编程思维方式，让我在短短一个月内，对写代码有了一个质的提升，所谓醍醐灌顶，莫不如是。

为什么选择Qt的源码，一来是工作所需，二来是Qt的源码看起来确实是通俗易懂。但是，因为我水平优先，源码中还有很多地方没有能够看懂，只能暂时记录，留待日后研究。

下面进入主题，本次只记录其中重要的部分，一些细枝末节和简单的代码跳过不提

# QThread

Qt的线程模块位于：**Qt的安装目录\5.5\Src\qtbase\src\corelib\thread** 下

QThread是Qt的线程模块，Qt中的线程，最常用的方法是，继承QThread类，并重写run()方法，start()之后，将会在线程中执行run()中的工作代码。

目录下，qthread.h对应两个cpp文件，分别是，qthread_win.cpp和qthread_unix.cpp，对应着两个操作系统，

### run()

```c++
virtual void run();
int exec();
```

```c++
void QThread::run()
{
    (void) exec();
}

int QThread::exec()
{
    Q_D(QThread);//d指针，为了使用成员数据
    QMutexLocker locker(&d->mutex);
    d->data->quitNow = false;
    if (d->exited) {
        d->exited = false;
        return d->returnCode;
    }
    locker.unlock();

    //进入事件循环，直到结束
    QEventLoop eventLoop;
    int returnCode = eventLoop.exec();

    locker.relock();
    d->exited = false;
    d->returnCode = -1;
    return returnCode;
}
```

QThread默认的run()方法，只有一个事件循环而已，你需要的是重写它

### void start(Priority = InheritPriority)

```c++
void start(Priority = InheritPriority);
```

未完，待续