# 一、PIMPL机制

**PIMPL** ，即Private Implementation，作用是，实现 **私有化，力图使得头文件对改变不透明**，以达到解耦的目的

> pimpl 用法背后的思想是把客户与所有关于类的私有部分的知识隔离开。由于客户是依赖于类的头文件的，头文件中的任何变化都会影响客户，即使仅是对私有节或保护节的修改。pimpl用法隐藏了这些细节，方法是将私有数据和函数放入一个单独的类中，并保存在一个实现文件中，然后在头文件中对这个类进行前向声明并保存一个指向该实现类的指针。类的构造函数分配这个pimpl类，而析构函数则释放它。这样可以消除头文件与实现细节的相关性

该句出自 ***超越 C++ 标准库--boost程序库 导论***

***该文的代码说明均忽略一些简单必须的代码，以保证示例的简洁，比如防止头文件重复包含等***

## （1）实例说明 

假设现在有一个需求，你需要写一个类，来完成产品的信息保存和获取，这个需求看起来非常的简单，我们只需要一分钟就能写好

**Product.h**

```c++
class Product
{
public:
	string getName() const;
    void setName(const string& name);
    float getPrice() const;
    void setPrice(float price);
private:
    string name;
    float price;
};
```

**Product.cpp**

```c++
string Product::getName() const
{
    return this->name;
}
void Product::setName(const string &name)
{
    this->name = name;
}
float Product::getPrice() const
{
    return this->price;
}
void Product::setPrice(float price)
{
    this->price = price;
}
```

当然，你可能会说，这个简单的代码根本不需要cpp，但是我们这里只是举个例子，实际的情况肯定比这复杂的多的多。

言归正传，我们完成了我们的模块，并交付出去提供给他人调用，结果第二天，有了新的需求，你需要新增一个成员变量，用作其中某个业务逻辑的数据存储，所以你不得不在头文件中的class内新增了一个成员属性，并在cpp中修改逻辑，辛运的是对外开放的接口并没有任何变动，调用你的模块的地方不需要修改代码。完成之后，交付使用。

然后这时候问题来了，调用此模块的人向你抱怨，替换了你的模块之后，明明自己没有修改任何东西，但是整个工程重新编译了整整半个多小时（可能有些夸张）。因为整个工程代码量巨大，很多地方都使用了你的模块，包含了你的头文件，导致这些包含你的头文件的地方虽然没有变动，但是都重新编译了。

利用PIMPL机制，将私有成员隐藏起来，使得只有接口不变，那么头文件就不会改变，已达到解耦的目的。从上面例子也可以看出，PIMPL机制的好处之一就是避免头文件依赖，提高编译速度。

那利用PIMPL机制，上面的问题如何解决呢？

## （2）利用PIMPL机制

基于原来的需求，代码设计如下：

**Product.h**

```c++
class ProductData;
class Product
{
public:
    Product();
    ~Product();
    
	string getName() const;
    void setName(const string& name);
    float getPrice() const;
    void setPrice(float price);
    
private:
    ProductData* data;
};
```

**Product.cpp**

```c++
class ProductData
{
public:
    string name;
    float price;
};

Product::Product()
{
    data = new ProductData();
}
Product::~Product()
{
    delete data;
}
string Product::getName() const
{
    return data->name;
}
void Product::setName(const string &name)
{
    data->name = name;
}
float Product::getPrice() const
{
    return data->price;
}
void Product::setPrice(float price)
{
    data->price = price;
}
```

可以看出来，Product 类除了必要的接口函数外，就只有一个ProductData指针了，而ProductData又是使用的前置声明，在cpp中实现，这样，只要接口不变，那么内部私有成员或者逻辑改变，并不会影响client

上面的代码只是最简单的实现，其中还存在很多问题，而实际的项目中可能要复杂的多。尽管如此，我们也能看出PIMPL机制的优点：

- 降低耦合度；
- 隐藏模块信息；
- 降低编译依赖，提高编译速度；
- 接口和实现真正分离；

# 二、Qt源码中的d指针

了解PIMPL机制之后，我们可以看看优秀的C++库中是如何实现PIMPL机制的，以Qt框架为例。读过Qt源码的同学对Qt中的d指针想必不会陌生，我们来详细讲解一下

## （1）QThread中的PIMPL机制

我们随便选取一个Qt中的模块，以QThread为例分析一下Qt中是如何实现PIMPL机制的

首先，找到QThread类的头文件 qthread.h，我们可以看到，QThread 类的什么中，除了对外的接口外，根本看不到能够猜测内部实现方法或者变量，而且其private的成员只有下面几个

```c++
class Q_CORE_EXPORT QThread : public QObject
{
    ...
private:
    Q_DECLARE_PRIVATE(QThread)

    friend class QCoreApplication;
    friend class QThreadData;
};
```

那么，QThread内部使用的方法和属性都去哪里了呢

我们先找到它的构造函数，实现如下：

```c++
QThread::QThread(QObject *parent)
    : QObject(*(new QThreadPrivate), parent)
{
    Q_D(QThread);
    d->data->thread = this;
}
```

## （2）Q_D宏

**Q_D()** 是Qt中的一个宏定义

```c++
#define Q_D(Class) Class##Private * const d = d_func()
#define Q_Q(Class) Class * const q = q_func()
```

**Q_D(QThread);** 展开如下：

```c++
QThreadPrivate * const d = d_func();
```

这也是上面代码中的 **d** 指针的由来，可以看到，d其实是一个QThreadPrivate指针，const标在d前面，类型后面，表示d指针的的指向不能改变，这点不懂的需要去复习一下const的用法，同理，q是QThread指针，且指向不能改变，所以，代码中出现下面的宏将会得到传入对象的指针

```c++
Q_D(QThread);		//QThread*
Q_D(const QThread);	//const QThread*
```

## （3）d_func()

这里有一个方法 **d_func()**，我们可以查看到它的声明

```c++
#define Q_DECLARE_PRIVATE(Class) \
    inline Class##Private* d_func() { \
    return reinterpret_cast<Class##Private *>(qGetPtrHelper(d_ptr));} \
    inline const Class##Private* d_func() const { \
    return reinterpret_cast<const Class##Private *>(qGetPtrHelper(d_ptr));} \
    friend class Class##Private;
```

上面这段代码会生成方法 d_func()，而在QThread 头文件类声明中，可以看到此宏

```c++
class Q_CORE_EXPORT QThread : public QObject
{
    ...
private:
    Q_DECLARE_PRIVATE(QThread)
	...
};

```

## （4）Q_DECLARE_PRIVATE宏

**Q_DECLARE_PRIVATE(QThread)** 宏展开如下：

```c++
class Q_CORE_EXPORT QThread : public QObject
{
    ...
private:
	inline QThreadPrivate* d_func(){
    	return reinterpret_cast<QThreadPrivate*>(qGetPtrHelper(d_ptr));
	}
	inline const QThreadPrivate* d_func() const {
    	return reinterpret_cast<QThreadPrivate*>(qGetPtrHelper(d_ptr));
	}
	friend class QThreadPrivate;
};
```

可以看出，这个宏其实是在QThread内部定义了两个 inline 方法和一个友元类，d_func() 方法也来源于此

## （5）qGetPtrHelper()方法

这里的 **qGetPtrHelper()** 方法可以找到，我给它重新排版一下，如下：

```c++
template <typename T> static inline T *qGetPtrHelper(T *ptr) { 
    return ptr; 
}
template <typename Wrapper> static inline typename Wrapper::pointer qGetPtrHelper(const Wrapper &p) { 
    return p.data(); 
}
```

这是一个模板方法，如果只是一个普通的类指针，则返回该指针；

而如果是一个类模板方法，则调用data()方法，并返回结果

所以，我们看到上面的 **d_func()** 方法中的，替换了 **qGetPtrHelper()** 方法后，如下：

```c++
class Q_CORE_EXPORT QThread : public QObject
{
    ...
	inline QThreadPrivate* d_func(){
    	return reinterpret_cast<QThreadPrivate*>(d_ptr.data());
	}
	inline const QThreadPrivate* d_func() const {
    	return reinterpret_cast<QThreadPrivate*>(d_ptr.data());
	}
};
```

## （6）d_ptr

那么，这里的 **d_ptr** 又是哪里来的呢，它其实是在 **QObject** 对象内定义的

```c++
class Q_CORE_EXPORT QObject
{
    ...
 protected:
    QScopedPointer<QObjectData> d_ptr;
};
```

我们都知道，Qt 中所有对象都是继承自 **QObject** 的，所以 **QThread** 是可以使用 d_ptr 的

**QScopedPointer** 是Qt中封装的智能指针，相当于stl中的std::unique_ptr，所以，上面代码中的 **d_ptr.data()** 作用是获取智能指针管理的指针，等同于std::unique_ptr中的 **get()** 方法，也就是这里的 **QObjectData** 指针。

所以，**d_func()** 方法的作用是，获取 **QThread** 中继承的 **QObject** 中的 **QObjectData** 指针，并使用强制类型转换为 **QThreadPrivate** 指针类型。而为什么能转换，因为他们之间是有继承关系的 **QThreadPrivate -> QObjectPrivate -> QObjectData** 

上面说的所有东西最终都是在分析 **Q_D(QThread);**，现在我们知道，这句宏定义最后会得到 **QThreadPrivate** 指针，而这个类的作用就是我们之前讲的 PIMPL机制中的用作保存 **QThread** 类私有成员，以达到解耦的目的

# 三、使用d指针

学完了Qt巧妙的d指针，我们在第一节中的代码中照葫芦画瓢引入d指针，最终代码如下：

**global.h**

```c++
template <typename T> static inline T *qGetPtrHelper(T *ptr) { return ptr; }
template <typename Wrapper> static inline typename Wrapper::pointer qGetPtrHelper(const Wrapper &p) { return p.get(); }

#define Q_DECLARE_PRIVATE(Class) \
    inline Class##Private* d_func() { return reinterpret_cast<Class##Private *>(qGetPtrHelper(d_ptr)); } \
    inline const Class##Private* d_func() const { return reinterpret_cast<const Class##Private *>(qGetPtrHelper(d_ptr)); } \
    friend class Class##Private;

#define Q_D(Class) Class##Private * const d = d_func()
#define Q_Q(Class) Class * const q = q_func()
```

**base.h**

```c++
class BaseData
{
public:
    BaseData(){}
    virtual ~BaseData(){}
};

class BasePrivate : public BaseData
{
public:
    BasePrivate(){}
    virtual ~BasePrivate() {}
};
```

**product.h**

```c++
#include "global.h"
#include "test_p.h"
#include <memory>
#include <string>

using namespace std;

class ProductPrivate;
class ProductData;

class Product
{
public:
    explicit Product(int num = 1);
    ~Product();
    string getName() const;
    void setName(const string& name);
    float getPrice() const;
    void setPrice(float price);
protected:
    Product(ProductPrivate* testPrivate, int num = 1);
protected:
    std::unique_ptr<BaseData> d_ptr;
private:
    Q_DECLARE_PRIVATE(Product)
    friend class ProductData;
};
```

**product.cpp**

```c++
#include "product.h"
#include <iostream>
using namespace std;

class ProductData : public BaseData
{
public:
    Product* test;
};

class ProductPrivate : public BasePrivate
{
public:
    ProductPrivate(ProductData* d = 0) : data(d) {
        if(!data) {
            data = new ProductData();
        }
    }
    ProductData* data;

    int number;
    string name;
    float price;
};

Product::Product(ProductPrivate *testPrivate, int num) : d_ptr(testPrivate)
{
    Q_D(Product);
    d->data->test = this;
}

Product::Product(int num)
{
    d_ptr = std::unique_ptr<ProductPrivate>(new ProductPrivate());
}

string Product::getName() const
{
    Q_D(const Product);
    return d->name;
}

void Product::setName(const string &name)
{
    Q_D(Product);
    d->name = name;
}

float Product::getPrice() const
{
    Q_D(const Product);
    return d->price;
}

void Product::setPrice(float price)
{
    Q_D(Product);
    d->price = price;
}
```

上面的代码其实并不是如何巧妙，里面加了一些东西，可以方便其他模块拓展，这里只是作为一个总结和参考

