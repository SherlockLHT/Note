[TOC]

# 说明

Qt中可以有多种使用线程的方式：

1. 继承 ***QThread***，重写 ***run()*** 接口；
2. 使用 ***moveToThread()*** 方法将 ***QObject*** 子类移至线程中，内部的所有使用信号槽的槽函数均在线程中执行；
3. 使用 ***QThreadPool*** 线程池，搭配 ***QRunnable***；
4. 使用 ***QtConcurrent***；

本文跳过第1和第2中方式，介绍后面两种

# 线程池

- 创建和销毁线程需要和OS交互，少量线程影响不大，但是线程数量太大，势必会影响性能，使用线程池可以这种开销；
- 线程池维护一定数量的线程，使用时，将指定函数传递给线程池，线程池会在线程中执行任务；

## （一）QThreadPool和QRunnable

Qt中需要继承 ***QRunnable***，重写 ***run()*** 方法，并，将其传递给线程池 ***QThreadPool*** 进行管理

### QRunnable常用接口

```c++
bool QRunnable::autoDelete() const;
void QRunnable::setAutoDelete(bool autoDelete)；
```

- ***QRunnable*** 常用函数不多，主要设置其传到底给线程池后，是否需要自动析构；
- 若该值为false，则需要程序员手动析构，要注意内存泄漏；

### QThreadPool常用接口

```c++
void QThreadPool::start(QRunnable * runnable, int priority = 0);
bool QThreadPool::tryStart(QRunnable * runnable);
```

- ***start()*** 预定一个线程用于执行QRunnable接口，当预定的线程数量超出线程池的最大线程数后，QRunnable接口将会进入队列，等有空闲线程后，再执行；

- priority指定优先级
- ***tryStart()*** 和 ***start()*** 的不同之处在于，当没有空闲线程后，不进入队列，返回false

```c++
void QThreadPool::cancel(QRunnable * runnable);
void QThreadPool::clear();
```

1. 如果，指定的QRunnable还没有执行，则从队列中移除
2. 清空队列中还没有执行的QRunnable；

```c++
bool QThreadPool::waitForDone(int msecs = -1);
```

- 等待所有线程结束并释放资源（如果需要自动释放的话）；
- msecs指定超时；
- 若所有线程都被移除，则，返回true，否则返回false；

```c++
int	maxThreadCount() const
void setMaxThreadCount(int maxThreadCount)
```

- 线程池维护的最大线程数量；
- 设定该值，不会影响已经开始的线程；
- 若没有设定，默认值是最大线程数，可以用：`QThread::idealThreadCount();` 获取；

```c++
static QThreadPool * QThreadPool::globalInstance();
```
- 全局内存池实例；
- 若创建QThreadPool实例，则在实例生存周期内，内存池有效，

### 代码示例

```c++
//MyRunnable.h
class MyRunnable : public QRunnable
{
public:
    MyRunnable(const QString& thread_name);
    void run();
private:
    QString threadName;
};
```

```c++
//MyRunnable.cpp
#include "myrunnable.h"
MyRunnable::MyRunnable(const QString &thread_name) : threadName(thread_name){}
void MyRunnable::run()
{
    qDebug()<<"Start thread id:"<<QThread::currentThreadId();
    int count = 0;
    while(true)
    {
        if(count >= 10)
        {
            break;
        }
        qDebug()<<threadName<<" Count:"<<count++;
        QThread::msleep(500);
    }
}
```

```c++
//调用处
MyRunnable* my_runnable = new MyRunnable("1# thread");
my_runnable->setAutoDelete(true);

MyRunnable* my_runnable_2 = new MyRunnable("2# thread");
my_runnable_2->setAutoDelete(true);

threadPool.start(my_runnable);
threadPool.start(my_runnable_2);
```

## （二）QtConcurrent

若有大量工作需要完成，则使用方式1、2、3均可，但是若只有一小段工作，需要在线程中完成，无论是使用QThread，还是moveToThread，更或者，使用QThreadPool，都有大材小用的感觉，这时候，使用 ***QtConcurrent*** 就是最佳选择

下面的说明，以Qt自带的例子为基础，并加入部分修改，例子目录：***..\Qt\Qt5.5.1_mingw\Examples\Qt-5.5\qtconcurrent\runfunction***

### pro文件中添加模块

```python
QT += concurrent
```

### 代码示例：

```c++
QString hello(QString name)
{
    qDebug() << "Hello" << name << "from" << QThread::currentThread();
    return name;
}
```

```c++
//掉用处
QFuture<QString> f1 = QtConcurrent::run(hello, QString("Alice"));
QFuture<QString> f2 = QtConcurrent::run(&threadPool, hello, QString("Bob"));
f1.waitForFinished();
f2.waitForFinished();

qDebug()<<f1.result();
qDebug()<<f2.result();
```

输出如下：

```c++
Hello "Alice" from QThread(0x1b1562a8, name = "Thread (pooled)")
Hello "Bob" from QThread(0x1b156248, name = "Thread (pooled)")
"Alice"
"Bob"
```

#### （1）说明：

```c++
QFuture<T> QtConcurrent::run(Function function, ...);
//QtConcurrent::run(QThreadPool::globalInstance(), function, ...);
QFuture<T> QtConcurrent::run(QThreadPool * pool, Function function, ...);
```

***QtConcurrent::run()*** 方法的第一个参数是线程池，可以指定线程池，若没有指定，则使用全局线程池；

#### （2）执行普通函数，并传参，获取返回值

参考上例

- 将需要传递的参数依次跟在函数名后面；
- 使用 ***QFuture\<T\>*** 的 ***result()*** 方法获取返回值；

#### （3）执行非const成员函数

```c++
QFuture<QString> f1 = QtConcurrent::run(this, &MainWindow::Hello, QString("Alice"));
QFuture<QString> f2 = QtConcurrent::run(&threadPool, this, &MainWindow::Hello, QString("Bob"));
```

- 线程池和传参以及返回值相同；

- 既然是成员，则需要指定实例，且是非const，则可能需要修改成员，在函数名前传入实例指针或者实例的应用；

#### （4）执行const成员

```c++
// call 'QList<QByteArray>  QByteArray::split(char sep) const' in a separate thread
QByteArray bytearray = "hello world";
QFuture<QList<QByteArray> > future = QtConcurrent::run(bytearray, &QByteArray::split, ',');
...
QList<QByteArray> result = future.result();
```

const成员不会使用非const成员，所以，传入指针/引用或或者形参并没有区别，上例中，就是传入了形参


