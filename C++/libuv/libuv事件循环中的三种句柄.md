# 1、说明

本文会简单介绍 ***libuv*** 的事件循环，旨在入门级别的使用，而不做深入探究，简单来说就是，会大概用就行，先用熟练了，再去探究原理和源码

下图为官网的 ***libuv*** 的不同部分及其涉及的子系统的图：

![img](C:\Users\sherlock\Desktop\architecture.png)

***libuv*** 使用 ***handles*** 和 ***requests*** 来结合使用事件循环

***handles***  表示能够执行某些耗时的长时间存在的对象

***requests*** 表示短暂的操作，可以在一个 ***handles*** 上执行

下图为官网的事件循环：

![_images / loop_iteration.png](C:\Users\sherlock\Desktop\loop_iteration.png)

这张图其实表明了 ***libuv*** 中的时间循环的处理过程，也就是 ***uv_run()*** 方法执行的过程，该方法内部是一个 ***while*** 循环：

1. 先判断循环是都处于活动状态，通过判断当前是否处于 alive 状态，来确定事件循环是否退出；
2. 运行倒计时定时器（维护所有句柄的定时器）；
3. 执行待执行的回调函数；
4. 运行 ***idle*** 句柄；
5. 运行 ***prepare*** 句柄；
6. 轮询 I/O；
7. 运行 ***check*** 句柄；
8. 调用 close 回调；

上述步骤中，有三个句柄被重点标出，我们就来讨论这三个句柄

# 2、idle句柄

***idle handle*** 即空闲句柄，从上面流程图上可以看出，如果启动了 ***idle handle***，每次事件循环的时候都会执行一遍其回调

## 2.1、uv_idle_init

该方法用于初始化 ***idle handle***

```c
int uv_idle_init(uv_loop_t* loop, uv_idle_t* idle)
```

***uv_idle_t*** 是 ***idle*** 句柄类型

该方法永远执行成功，返回值0

## 2.2、uv_idle_start

该方法用于开始 ***idle handle***

```c
int uv_idle_start(uv_idle_t* idle, uv_idle_cb cb)
```

该方法用于都是执行成功的（返回值0），除非回调函数设置为 NULL（此时返回 ***UV_EINVAL***）

回调函数声明如下：

```c
void (*uv_idle_cb)(uv_idle_t* handle);
```

回调函数会把句柄带过去

## 2.3、uv_idle_stop

该方法用于停止 ***idle handle***

```c
int uv_idle_stop(uv_idle_t* idle)
```

该方法永远执行成功，返回值0

执行之后，回调不会再执行

# 3、prepare句柄

可以理解成准备句柄，从流程图中可以看出，在 ***idle*** 之后，在轮询 IO 之前执行其回调

其API和 ***idle*** 差不多

## 3.1、uv_prepare_init

```c
int uv_prepare_init(uv_loop_t* loop, uv_prepare_t* prepare);
```

初始化句柄，***uv_prepare_t*** 为 ***prepare*** 句柄类型

返回值0，总是成功的

## 3.2、uv_prepare_start

```c
int uv_prepare_start(uv_prepare_t* prepare, uv_prepare_cb cb);
```

开始句柄，执行总是成功的（返回0），除非回调函数为 NULL（此时返回 UV_EINVAL ）

```c
void (*uv_prepare_cb)(uv_prepare_t* handle);
```

## 3.3、uv_prepare_stop

```c
int uv_prepare_stop(uv_prepare_t* prepare);
```

停止句柄，回调函数不会再执行

# 4、check句柄

可以理解为检查句柄，如果程序中启动了 ***check*** 句柄，则在每次轮询 IO 之后执行其回调函数，正好和 ***prepare*** 前后呼应

这种设计的机制是 ***libuv*** 为用户预留的借口，在轮询 IO 循环状态前后进行准备和校验操作

其 API 和上面两种句柄类似

## 4.1、uv_check_init

```c
int uv_check_init(uv_loop_t* loop, uv_check_t* check);
```

初始化句柄，***uv_check_t*** 为 ***check*** 句柄类型

方法执行总是成功的

## 4.2、uv_check_start

```c
int uv_check_start(uv_check_t* check, uv_check_cb cb);
```

开始句柄，回调函数可以为 NULL

方法执行总是成功的（返回0），除非回调函数为 NULL（返回UV_EINVAL ）

```c
void (*uv_check_cb)(uv_check_t* handle);
```

## 4.3、uv_check_stop

```c
int uv_check_stop(uv_check_t* check);
```

停止句柄，回调函数不会再执行

方法执行总是成功的，返回0

# 5、代码示例

```c
#include <stdio.h>
#include <stdlib.h>
#include <uv.h>

#define MAX_NUM 3

int count = 0;
void idle_cb(uv_idle_t *handle)
{
    count++;
    printf("idle handle callback, count = %d\n", count);
    if (count >= MAX_NUM)
    {
        printf("idle handle stop, count = %d\n", count);
        uv_stop(uv_default_loop());
    }
}

void prepare_cb(uv_prepare_t *handle)
{
    printf("prepare handle callback\n");
}

void check_cb(uv_check_t *check)
{
    printf("check handle callback\n");
}

int main()
{
    uv_idle_t idle;
    uv_prepare_t prepare;
    uv_check_t check;

    uv_idle_init(uv_default_loop(), &idle);
    uv_idle_start(&idle, idle_cb);

    uv_prepare_init(uv_default_loop(), &prepare);
    uv_prepare_start(&prepare, prepare_cb);

    uv_check_init(uv_default_loop(), &check);
    uv_check_start(&check, check_cb);

    uv_run(uv_default_loop(), UV_RUN_DEFAULT);

    return 0;
}

```

输出结果如下：

```
idle handle callback, count = 1
prepare handle callback
check handle callback
idle handle callback, count = 2
prepare handle callback
check handle callback
idle handle callback, count = 3
idle handle stop, count = 3
prepare handle callback
check handle callback
```

上例子中没有 IO 相关的代码，主要用于熟悉三种句柄回调函数的执行顺序