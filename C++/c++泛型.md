

泛型编程 **（Generic Programming）** 是一种语言机制，针对广泛类型的编程方式。是一种基于发现高效算法的最抽象表示的编程方式，即以算法为起点并寻找能使其工作并且有效率的工作的最一般的必要条件集。

C++用模版来实现泛型变成，模版分为 **函数模版** 和 **类模版** ，模版也叫 **参数类型多态化** ，泛型在编译时期确定， **相比于面向对象的虚函数多态，能够有更高的效率** 。

C++泛型编程广泛应用于STL

# 1、函数模版（function template）

**示例：**

原函数：

```c++
int add(int a, int b) {return a+b;} 
float add(float a, float b){return a+b;} 
...
```

C++重载函数可以做到，缺点：

- 重复写相同算法比较辛苦；
- 函数重载是静态编译，运行时占据过多的内存；

模版函数：

```c++
template<typename T>//设定一个泛型类型T 
T add(T a, T b)	//注意形参和返回值 
{    
    return a + b; 
}
```

调用：

```c++
add(3, 5);  		//8 
add(3.1, 5.1);  	//8.2 
add(3.0, 5);		//编译错误，参数类型不一致 
add<int>(3,5); 		//8，指定类型 
add<int>(3.6, 5.5); //8，先类型转换
```

# 2、类模版（class template）

最典型的示例就是容器。

需要注意的是cpp文件中方法的类类型

**示例：**

```c++
//这里typename和class等价，用typename的原因是避免混淆
template <typename T>
class Operate
{
public:
    void SetData(T a, T b);
    T GetA();
    T GetB();
private:
    T dataA;
    T dataB;
}
```

```c++
void Operate<T>::SetData(T a, T b)
{
    dataA = a;
    dataB = b;
}
T Operate<T>::GetA(){ return dataA; }
T Operate<T>::GetB(){ return dataB; }
```

调用：

**与函数模版不同，类模版声明必须指定实参类型**

```c++
Operate<string> test; 
test.SetData("aaa", "bbb"); 
test.GetA();	//"aaa" 
test.GetB();	//"bbb"
```

# 3、非类型参数

**示例：**

```c++
template<class T, int n>
class MyArray
{
private:
    T array[n];
public:
    MyArray();
    explicit MyArray(const T& value);
    T & operator[](int index);
    T operator[](int index) const;
};
```

```c++

template<class T, int n>
MyArray<T, n>::MyArray(){}

template<class T, int n>
MyArray<T, n>::MyArray(const T &value)
{
    for(int index = 0; index < n; index++)
    {
        array[index] = value;
    }
}

template<class T, int n>
T &MyArray<T, n>::operator[](int index)
{
    if(index < 0 || index >= n)
    {
        std::exit(EXIT_FAILURE);
    }
    return array[index];
}

template<class T, int n>
T MyArray<T, n>::operator[](int index) const
{
    if(index < 0 || index >= n)
    {
        std::exit(EXIT_FAILURE);
    }
    return array[index];
}
```

**说明：**

```c++
template<class T, int n>
```

使用class指出T的类型，int指出n的类型为int，这个n就是非类型参数，即它指定了类型，不作为泛型名

使用如下：

```c++
MyArray<int, 5> array(123);
```

# 4、模版的多功能性

模板类可以用作基类、组件类、还可以用作其他模板的类型参数

```c++
template <class T>
class Array{ ... };

//类模板作为基类
template <class Type>
class GrowArray : public Array<Type> { ... };

//模板类作为组件
template <class Tp>
class Stack{
    Array<Tp> array;
};

//模板类作为另一个模板类的类型参数
Array<Stack<int> > array;	//c++98需要在两个>之间加上空避免和>>混淆，c++11不需要
```

## 4.1 递归使用模板

比如上面的例子中，可以这样使用

```c++
Array<Array<int>> arrays;
```

这表示，arrays是一个Array数组，其中，每个元素都是Array数组，其中的每个元素都是int型，这类似一个二维数组

## 4.2 使用多个类型参数

上面例子中定义的模板类都只有一个类型，其实也可以定义多个

```c++
template<class T1, class T2> class MyClass { ... } 
//使用时 
MyClass<int, float> mc;
```

# 5、模板具体化

因为模板的定义并没有具体的类型，所以使用具体的类型生成具体的类定义，称之为模板的具体化，有 **显示实例化、隐式实例化和显示具体化** 三种。

## 5.1 隐式实例化

当使用模板定义对象时，传入所需要的类型，编译器就使用通用模板根据传入的类型生成具体的类定义，然后再创建对象。因为此过程的目的是创建对象，而具体化类只是这个过程中附带的，所致称之为隐式实例化。

```c++
MyClass<int> test; //隐式实例化
```
编译器在需要对象之前，不会生成类的隐式实例化，示例如下：
```c++
//另一种隐式实例化
MyClass<int>* test;			//定义一个指针，没有隐式实例化
test = new MyClass<int>;	//需要一个对象
```

第二条语句导致编译器生成类定义，并创建一个对象。

## 5.2 显示实例化

使用 template 关键字指出模板所需要的类型，可实例化模板

```c++
template class MyClass<int>;	//显示实例化
```

## 5.3 显示具体化

暂略

## 5.4 部分具体化

暂略

# 6、成员模板

模板可以作为结构体、类和模板类的成员

# 7、将模板用作参数

模板的参数可以是类型参数、非类型参数，也可以是模板参数，即模板可以作为模板的参数

```c++
template<template <typename T> class Thing>
class MyClass{
    ...
};
```

上例子中，**template \<typename T> class** 是一个类型，Thing是一个参数

假设有以下声明

```c++
MyClass<MyArray> test;
```

只有当 MyArray 的声明如下时，上面的声明才是正确的

```c++
template<typename T>
class MyArray{
    ...
};
```

# 总结

- 函数模版并不是函数，是C++编译生成具体函数的一个模版；
- 函数模版不是只编译一份满足多份，而是每一种替换它的函数编译一份；
- 函数模板不允许自动类型转换；
- 函数模板不可以设置默认模版实参，例如:template\<typename T=0>就是错误的
- 同理，类模版也不是真正的类，而是C++编译器生成具体类的一个模板；
- 类模版可以设置默认模板实参；

# 模板未完，待续