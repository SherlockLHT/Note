Qt进行一些耗时的操作会导致界面的绘制事件不再循环，从而导致界面卡死，解决这种问题有两种方法：一是将耗时的操作放在线程种，二是使用`QApplication::processEvents()`，

## QApplication::processEvents()

在耗时操作种不间断调用此函数即可解决事件不循环的问题。

```c++
void QCoreApplication::processEvents(QEventLoop::ProcessEventsFlags flags = QEventLoop::AllEvents)
```

- 该函数的作用是让程序处理还没有处理的事件；
- **flags** 指定处理去处理的事件类型，不指定则默认全部类型事件；

#### 注意：

该操作会导致 **DeferredDelete** 事件不会执行，使用时要注意

> In the event that you are running a local loop which calls this function continuously, without an event loop, the DeferredDelete events will not be processed. This can affect the behaviour of widgets, e.g. QToolTip, that rely on DeferredDelete events to function properly. An alternative would be to call sendPostedEvents() from within that local loop.

