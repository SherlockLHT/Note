# 特征

内敛函数和常规函数的区别在于编译方式

- 内联函数的代码是使用宏定义实现的；

- 相当于用内敛函数的函数体替代函数调用；
- 无参数压栈、栈帧开辟与回收、结果返回等操作；
- 类似宏定义，但是比宏定义多了类型检查，更安全；
- 只能有一些简单的语句，不能执行循环、递归、switch、递归等复杂语句；
- 若类方法直接在声明中定义，则其会被隐式地当成inline函数（虚函数除外）；

# 使用方法

请求使用内联函数只需要下面的修饰方式其中之一即可：

- 函数声明的前面使用inline修饰；
- 函数实现的前面使用inline修饰；

通常做法是将实现和声明写在一起

# 示例

```c++
//使用inline修饰，建议使用
inline int function(int value);	
```
```c++
//函数实现前使用inline修饰
int function();
inline int function(){ return 123; }
```

```c++
//类方法中隐式内联
class Test{
  	int function(){ return 123; }  
};
```

```c++
//类方法中的显示内联
class Test{
    int function();
};
inline int Test::function() { return 123; }
```

# 优缺点

## 优点

1. 运行速度比常规函数快；
2. 像常规函数一样做类型检查，比宏定义安全；
3. 可以访问类成员，宏定义不能；
4. 在运行时调用，宏在编译期决定；

## 缺点

1. 需要占用的内存比常规函数多，每次调用的地方等同于包含了一个副本；
2. inline函数改变需要重新编译，常规函数只要接口不变，不需要重新编译；
3. 内联只是对编译器的建议，决定权在编译器，甚至有些编译器不支持内联；

# 虚函数和内联函数

虚函数（virtual）可以是内联函数吗？

虚函数可以是内联函数（**inline virtual** ），但是当虚函数表现为多态时，不能内联。因为内联是建议编译器内联，但是函数的多态性表现在运行期，编译器无法知道运行期调用哪个代码。因此，只有当编译器知道调用的对象是哪个的时候才会内联，即只有在编译器具有实际对象，而不是对象指针或者引用时才能内联

**示例：**

```c++
class Base
{
public:
    inline virtual void Test()
    {
        cout << "I am Base\n";
    }
    virtual ~Base() {}
};
class Derived : public Base
{
public:
    inline void Test()  // 不写inline时隐式内联
    {
        cout << "I am Derived\n";
    }
};
```

**客户端：**

```c++
//具体对象，编译器知道是哪个类，所以可以内联
Base base;
base.Test();

//指针调用，呈现多态性，需要在运行时才能确定
Base* ptr = new Derived();
ptr->Test();
```

