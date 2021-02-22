转载：https://www.cnblogs.com/cxq0017/p/10643466.html

# 1、说明

一个类的实例化对象所占空间的大小？ 注意不要说类的大小,是类的对象的大小。 首先，类的大小是什么？确切的说，类只是一个类型的定义，它是没有大小可言的，用sizeof运算符对一个类型名操作，得到的是具有该类型实体的大小

# 2、类sizeof()

注，以下程序均以x86下的32位程序作为参考

## 2.1、空类

空类占用的内存大小为1个字节

```c++
class A
{};

int main()
{
    A obj;
    int nLen = sizeof(obj);
    cout << nLen << endl; //sizeof(一个空类)为什么等于1？

    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402161117109-1144552269.png)

可以看到一个空类对象的大小1.

**一个空类对象的大小是1，为什么不是0？**

是因为每个实例在内存中都有一个独一无二的地址，为了达到这个目的，编译器会给一个空类隐含加一个字节

## 2.2、类成员方法

```c++
class A
{
public:
    void func1() { };
    void  func2() { };
    void  func3() { };
};

int main()
{
    A obj;
    int nLen = sizeof(obj);
    cout << nLen << endl; //sizeof(一个空类)为什么等于1？

    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402163010147-146646101.png)

可见，**成员函数不占用类对象的内存空间**

## 2.3、成员变量

```c++
class A
{
public:
    void func1() { };
    void  func2() { };
    void  func3() { };
    char ab;
};

int main()
{
    A obj;
    int nLen = sizeof(obj);
    cout << nLen << endl; //

    obj.ab = 'c';
    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402163416828-2003002444.png)

```c++
class A
{
public:
    void func1() { };
    void  func2() { };
    void  func3() { };
    //char ab;
    int nab;
};

int main()
{
    A obj;
    int nLen = sizeof(obj);
    cout << nLen << endl; //

    //obj.ab = 'c';
    obj.nab = 12;
    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402164552948-1653692950.png)

对比上面两个例子，可以发现，

1. 类成员变量会占用类对象的内存空间；
2. 如果不是空类，则编译器不需要隐含分配一个字节的内存；

## 2.4、虚函数

```c++
class A{};
class B{};
class C : public A
{
    virtual void func() = 0;
};

class D : public B, public C
{};

int main()
{
    cout << sizeof(A) << endl;
    cout << sizeof(B) << endl;

    cout << sizeof(C) << endl;
    cout << sizeof(D) << endl;

    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402200008900-1752439635.png)

类C因为有虚函数，所有实例之后，有 ***虚函数表***，所以大小为4

类D有B和C派生而来，大小应该是5，为什么是8呢？因为内存对齐

## 2.5、静态成员

```c++
#include <iostream>

using namespace std;

class A
{
public:
    A(int a) { x = a;  }
    void func(){ cout << x << endl; }
    ~A() { }
private:
    int x;
    int g;
};

class B
{
public:
private:
    int a;
    int b;
    static int xs;
};

int B::xs = 20;

int main()
{
    A a(10);
    //a.func();

    B b;
    cout << sizeof(a) << endl;
    cout << sizeof(b) << endl;
    return 0;
}
```

![img](https://img2018.cnblogs.com/blog/1019006/201904/1019006-20190402194902276-915717880.png)

上例子中，可以发现，静态成员是不占用对象的内存空间的

# 3、结论

综合第二节中的例子，可以得出C++中类对象所占空间大小的几点规律：

1. 类对象所占空间为其内部所有非静态成员数据类型大小之和（受内存对齐影响）；
2. 若果虚函数，则有虚函数表，多一个指针的大小；
3. 空类会默认分配一个字节内存；
4. 成员函数（包括构造函数和析构函数）均不占类对象空间；