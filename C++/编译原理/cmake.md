# 关于CMake

根据makefile一文中所描述的那样，对于达到一定代码量的工程，使用makefile会简化编译，但是，工程的代码量变得更大，makefile的文件依赖关系会变得很复杂，写起来也会是一件很头疼的事情，make工具即在此基础上出现，它能根据一定的语法生成Makefile文件，并且无关乎平台，做到了一次编写，到处编译。

make工具有很多，Qt的qmake就是其中一种，其能根据pro文件生成Makefile，但使用最多的是CMake

CMake是一个跨平台的编译工具，它不会直接编译代码，而是可以根据编译器的不同生成相应的Makefile或者project文件，还能够测试编译器所支持的c++特性。CMake的使用需要 **CMakeLists.txt** 文件，而这个 **CMakeLists.txt** 就是无关乎平台的。

# CMake的简单使用

- 编写好 **CMakeLists.txt** 后，执行 **cmake .** 指令即可，后面的参数其实是路径，cmake工具会在改路径下找 **CMakeLists.txt** 文件；
- windows下可以使用cmake-gui工具替代指令操作；
- 使用指令行的话，可以使用 **-G** 指令指定编译器；

例如，以下指令即会生成MinGW使用的Makefeil，而 **CMakeLists.txt** 在当前目录下查找

```cmake
cmake -G"MinGW Makefiles" .
```

- MakeLists.txt的语法包括：命令、注释和空格；

- 命令的参数包含在括号内，多个参数之间用空格间隔；
- 命令不区分大小写；

# 使用示例

## 1、编译单个文件

```c++
#include <iostream>
int main(int argc, char *argv[])
{
    unique<int> temp = unique<int>(new int(10));
    std::cout<<"hello"<<std::endl;
    return 0;
}
```

CMakeLists.txt文件如下：

```cmake
# CMake的最低版本要求
cmake_minimum_required (VERSION 3.16)
# 项目名称
project (TestMain)
# 添加c++11的支持
add_definitions(-std=c++11)
# 生成目标
add_executable (main main.cpp)
```

1. **cmake_minimum_required** 表示运行此文件的CMake的最低版本；
2. **project** 表示此项目的名称
3. **add_executable** 表示编译 **main.cpp** 文件，目标名称是 **main**；

编写完成之后，执行以下指令即可编译：

```cmake
cmake -G"MinGW Makefiles" ./
make
```

## 2、编译多个文件

例子中只有一个代码文件，若同级目录下有多个代码文件互相包含，可以修改如下的命令：

```cmake
add_executable (main main.cpp Temp.cpp)
```

这样的话，若文件比较多，写起来也会很麻烦，所以可以使用 **aux_source_directory** 命令，该命令会查找指定执行目录下的所有源文件，将文件名保存在变量中。

**aux_source_directory** 需要传两个参数，分别是：需要查找相对路径、变量名

**CMakeLists.txt** 文件内容如下：

```cmake
cmake_minimum_required (VERSION 3.16)
project (TestMain)
add_definitions(-std=c++11)
# 查找./路径下的所有文件，保存在变量DIR_SRC中
aux_source_directory(./ DIR_SRC)
# ${变量名} 使用变量
add_executable (main ${DIR_SRC})
```

# cmake和configure

在linux下使用源码安装的时候，有时候，会出现configure，它和cmake的作用差不多，但是用法不一样

configure就是执行目录IAEA的一个configure的shell脚本，生成Makefile

cmake则是一个跨平台的工具，它根据CMakeLists.txt来生成Makefile的

# 常用的CMakeLists.txt写法

cmake有一些特定的参数

```cmake
PROJECT_SOURCE_DIR		#项目源代码路径
PROJECT_BINARY_DIR		#默认的二进制文件的路径
EXECUTABLE_OUTPUT_PATH	#可执行文件的输入路径，可重设
LIBRARY_OUTPUT_PATH		#生成的库文件路径，可重设
```

常用写法：

```cmake
cmake_minimum_required (VERSION 3.16)	#CMake的最低版本要求
project (TestMain)		#项目名称

add_definitions(-std=c++11)	#添加c++11的支持

add_subdirectory(./Temp)#添加Temp文件夹编译，方式由Temp文件夹内的CMakeLists.txt负责

aux_source_directory(./ DIR_SRC)	#取出路径下的所有文件，赋值给变量
add_executable (main ${DIR_SRC})	# 生成目标，文件使用变量
set(EXECUTABLE_OUTPUT_PATH ./bin)	#重新设置可执行文件的输出路径

set(DIR_SRC main.cpp temp.cpp add.cpp)	#显式地设置变量
MESSAGE(STATUS "path:" ${DIR_SRC})		#打印信息，STATUS是前面的"-- "

include_directories(${PROJECT_SOURCE_DIR}/include)	#添加头文件路径
link_directories(${PROJECT_SOURCE_DIR}/libs)		#添加链接库路径
#添加依赖库，第一个参数是项目名，后面跟依赖库名，不用加后缀，多个库用空格隔开
target_link_libraries(TestMain ws2_32)	

# 生成依赖库test，第二参数分别表示动态库/静态库/dyld插件，第三个参数是文件
add_library(test SHARED/STATIC/MODULE test.cpp)
set(LIBRARY_OUTPUT_PATH ./lib)		#重设lib文件的输出路径
# 设置动态库版本号，VERSION指动态库版本，SOVERSION指API版本
set_target_properties(test PROPERTIES VERSION 1.2 SOVERSION 3)

#判断平台
IF (WIN32)
	MESSAGE(STATUS "Now is windows")
ELSEIF (APPLE)
	MESSAGE(STATUS "Now is Apple systens.")
ELSEIF (UNIX)
	MESSAGE(STATUS "Now is UNIX-like OS's.")
ENDIF ()
```

