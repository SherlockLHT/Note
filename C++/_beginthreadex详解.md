[TOC]

## 说明

C/C++编程中，创建线程，推荐优先使用 ***_beginthreadex*** 而不是 ***CreateThread***

## _beginthreadex

### 头文件

```c++
#include <process.h>
```

### 参数说明

```c++
unsigned long _beginthreadex( 
    void *security, 
    unsigned stack_size, 
	unsigned ( __stdcall *start_address )( void * ),
 	void *arglist, 
    unsigned initflag, 
    unsigned *thrdaddr 
);
```

***security*** ：线程的安全属性，NULL表示默认安全属性

***stack_size***：线程的堆栈大小，一般默认0

***start_address***：启动函数地址

***arglist***：参数列表，传递多个参数时用结构体，传结构体指针

***initflag***：新线程的初始状态，0表示立即执行，CREATE_SUSPEND（0x00000004）表示挂起

***thrdaddr***：用来接收线程ID

返回值：新线程句柄，失败返回0

