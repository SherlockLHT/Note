DLL是windows平台下的文件，调用方式为了动态加载和静态链接（有的地方又称显示调用和隐式调用），静态链接方式可参见相关文章，本文章主要记录动态加载的方法。

- 动态加载需要.dll文件，以及dll内导出函数的说明若是普通函数，数据结构都是通用结构，则只需要dll，和调用函数的说明；

- 若有自定义的类型，则需要和dll中的该类型相同的声明定义，简单的说，有头文件最好。使用平台API或者库API加载dll其中的函数，然后再使用，本文主要介绍使用Qt库函数的方法，该方法支持跨平台。

所谓动态加载就是在程序运行时进行加载，能否调用dll中的方法，只有在程序运行的时候才能知道。

### 使用dll中的函数

Qt提供QLibrary类提供动态加载。

示例：

```c++
//导出的函数
int DllAdd(int num, int num_2);
```

```c++
//动态加载
typedef int (*AddFunction)(int, int);	//定义函数指针，参数和返回值必须和导出的函数一致

QLibrary library("DllName.dll");
if(library.load())	//加载dll
{
    AddFunction Add = (AddFunction)library.resolve("DllAdd");	//提取指定函数
    if(Add)	//导出成功
    {
        qDebug()<<Add(1, 2);	//使用函数
    }
    else
    {
        qWarning()<<library.errorString();
    }
}
else //加载失败
{
    qWarning()<<library.errorString();
}
```

### 使用dll中类

由上例可见，平台和库API只能加载dll中的函数，若dll中导出类，则只能静态链接，无法动态加载。

为了能使用动态加载的方式使用dll中的类，则可以让dll中导出对象指针。

但是在加载dll函数的时候，需要定义函数指针，这时候，返回值是自定义的类，不是通用数据类型，所以此时可以将dll的类头文件加进来，以完成自定义数据类型的声明，但是这样就违反了OOP的封装性原则，也为模块设计带来诸多不变，为此，我们可以利用OOP语言的多态和继承的特性，使用抽象类来完成此功能，操作如下：

1. 声明抽象类及其中的接口函数；
2. 添加需要导出的类，继承自抽象类；
3. 加载dll的代码中，将抽象类的文件包含进去，定义抽象指针，new出需要导出的类的对象；
4. 程序结束释放内存。

这样做，只要抽象类文件，导出类无论如何更改，只要接口声明不变，都不会对调用方造成影响。

示例代码如下：

```c++
//Interface.h
class Interface
{
public:
    virtual int Add() = 0;
    virtual void SetSum(int sum) = 0;
    virtual int GetSum() = 0;
}
```

```c++
//Manager.h
//Manager.cpp略
class Manager : public Interface
{
public:
    Manager();
    ~Manager();
    
    int Add();
    void SetSum(int sum);
    int GetSum();
}
```

```c++
//Export.h
#define DLL_EXPORT extern "C" _declspec(dllexport)
DLL_EXPORT Interface* GetInstance();	//获取类指针
DLL_EXPORT void ReleaseInstance();		//释放资源，也可以在调用端delete指针释放资源
```

```c++
//Export.cpp
std::vector<Interface*> vecAllResource;
Interface* GetInstance()
{
    Interface* manager = new Manager();
    vecAllResource.push_back(manager);
    return manager;
}
void ReleaseInstance()
{
    for(int index = vecAllResource.size() - 1; index >= 0; index--)
    {
        delete vecAllResource.at(index);
    }
}
```

如上，在调用端，需要将`Interface.h`包进来即可，使用`GetInstance()`方法获取类指针。