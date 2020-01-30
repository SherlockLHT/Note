本文参考：[跟我一起写Makefile](https://seisman.github.io/how-to-write-makefile/overview.html)、[一文入门Makefile](https://zhuanlan.zhihu.com/p/56489231)

总结几种必备的makefile写法

## 一、makefile简介

## 二、makefile文件

一般以 **makefile** 或者 **Makefile** 命名，若使用此名称，则直接使用 **make** 指令即可。

若以其他名称命名，则使用 **make** 指令时，需要使用 **-f** 参数，指定文件

```python
make -f makefile_custom
```

## 三、基本语法

以下示例均使用此示例代码：

**main.cpp**

```c++
//main.cpp
#include "calc.h"
int main(){
    Calc* calc = new Calc();
    calc->print();
    delete calc;
    return 0;
}
```

**calc.h/.cpp**

```c++
//calc.h
class Temp;
class Calc
{
public:
	Calc();
	~Calc();
	void print();
private:
	Temp* temp;
};

//calc.cpp
#include "calc.h"
#include "temp.h"

Calc::Calc(){ temp = new Temp(); }
Calc::~Calc(){ delete temp; }
void Calc::print()
{
    std::cout<<"calc print"<<std::endl;
    temp->print();
}
```

**temp.h/.cpp**

```c++
//temp.h
class Temp{
public:
    Temp();
    ~Temp();
    void print();
};
//temp.cpp
#include "temp.h"
#include <iostream>

Temp::Temp(){}
Temp::~Temp(){}
void Temp::print()
{
    std::cout<<"temp print"<<std::endl;
}
```

### （1）基本规则

```makefile
目标： 依赖
	指令
```

- 指令前面是 **tab**；
- 若依赖不存在，或者修改时间比目标新，则执行指令，重新生成；
- 如果依赖或者指令太长，可以使用 **\\** 换行；

 所以，示例代码的 **makefile** 可以写成

```makefile
main: calc.o main.cpp
	g++ main.cpp calc.o temp.o -o main
calc.o: temp.o
	g++ -c calc.cpp temp.o
temp.o:
	g++ -c temp.cpp
```

### （2）clean

- **clean** 指令一般用于清理工程，然后重新编译；
- 每个makefile都应该写一个clean，这样便于重新编译，也利于保持文件清洁；

加上clean，makefile内容变成如下：

```makefile
main: calc.o main.cpp
	g++ main.cpp calc.o temp.o -o main
calc.o: temp.o
	g++ -c calc.cpp temp.o
temp.o:
	g++ -c temp.cpp

.PHONY : clean
clean:
	-rm temp.o calc.o main.exe
```

使用指令 **make clean** 清理即可

1. 当当前目录下存在 **clean** 文件的时候，该指令不会执行，使用 **.PHONY** 即可，表示这是一个“伪目标”；
2. **rm** 前面加上一个 **-**，表示忽略指令执行的错误；

### （3）使用变量

```makefile
DEL_FILE = rm

-$(DEL_FILE) temp.o calc.o main.exe
```

使用 **$(变量)** 即可以使用变量

加上变量后，makefile的内容变成

```makefile
DEL_FILE	= rm
RESULT_file	= temp.o calc.o main.exe

main: calc.o
	g++ main.cpp calc.o temp.o -o main

calc.o: calc.cpp temp.o
	g++ -c calc.cpp temp.o

temp.o: temp.cpp
	g++ -c temp.cpp

.PHONY : clean
clean:
	-$(DEL_FILE) $(RESULT_file)
```

### （4）自动变量

makefile提供了很多自动变量，这里列出几个常用的

1. **$@** ：规则中的目标
2. **$<** ：规则中的第一个依赖条件
3. **$^** ：规则中的所有依赖条件

这些自动变量只能在指令中使用

加上自动变量，makefile的内容变成如下：

```makefile
DEL_FILE	= rm
RESULT_file	= temp.o calc.o main.exe

main: calc.o
	g++ main.cpp calc.o temp.o -o $@
calc.o: calc.cpp temp.o
	g++ -c $^
temp.o: temp.cpp
	g++ -c $< -o temp.o

.PHONY : clean
clean:
	-$(DEL_FILE) $(RESULT_file)
```

### （5）自动推导

makefile支持自动推倒，即添加目标后，会自动添加同名的文件依赖，即添加 **main** 后，会自动添加依赖main.cpp或main.c

所以，makefile内容如下：

```makefile
DEL_FILE	= rm
RESULT_file	= temp.o calc.o main.exe

main: calc.o
	g++ main.cpp calc.o temp.o -o $@
calc.o: temp.o
	g++ -c calc.cpp $^
temp.o:
	g++ -c temp.cpp

.PHONY : clean
clean:
	-$(DEL_FILE) $(RESULT_file)
```

### （6）其他

makefile还指出条件判断、函数等，这里不做具体介绍

## 总结

makefile的功能很多，具体整理起来估计很耗时间，而且项目一旦变大，makefile也会变得复杂，写起来会狠麻烦，这时候就需要其他功能，比如cmake等，qt的qmake也是生成makefile的工具之一，qmake就是根据.pro文件生成makefile。

关于cmake的用法，参考下一篇文章