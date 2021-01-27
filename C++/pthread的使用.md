# 1、说明

***pthread*** 是Linux下的线程库。

# 2、使用

使用 ***pthread*** 需要添加头文件，并链接库 ***pthread***

```c
#include <pthread.h>
```

## 2.1、pthread_create

**声明：**

```c++
int pthread_create(pthread_t* thread, 
                   const pthread_attr_t* attr, 
                   void*(*start_routine)(void*), 
                   void* arg);
```

**参数：**

***pthread_t*** 定义如下：

```c
typedef unsigned long int pthread_t;
```

***thread*** 是一个指向线程标识符的指针，线程调用后，改值被设置为线程ID

***attr*** 用来设置线程属性

***start_routine*** 是线程函数的其实地址，即线程函数体，线程创建成功后，***thread*** 指向的内存单元从该地址开始运行

***arg*** 是传递给线程函数体的参数

**返回值：**

若线程创建成功，则返回0，失败则返回错误码，并且 **thread** 内容是未定义的。

## 2.2、pthread_join

**声明：**

```c
int pthread_join(pthread_t thread, void **retval);
```

**参数：**

***thread*** 是线程表示符

***retval*** 用来获取线程的返回值，一般是 ***pthread_join*** 方法传递出来的值

**说明：**

这是一个线程阻塞函数，调用该函数则等到线程结束才继续运行

## 2.3、pthread_exit

**声明：**

```c
void pthread_exit(void *retval);
```

**参数：**

***retval*** 是线程的退出码，传递给创建线程的地方

**说明：**

一个线程的结束有两种途径：

1. 线程函数体执行结束；
2. 调用 ***pthread_exit*** 方法退出线程；

## 2.4、pthread_self()

用来获取当前线程ID

**声明：**

```c
pthread_t pthread_self();
```

## 2.5、pthraad_detach

分离线程

**声明：**

```c
int pthread_detach (pthread_t __th)
```

# 3、线程属性

设置线程不同属相有不同属性有不同的方法，但是都需要先初始化属性数据结构，初始化函数为：

```c
int pthread_attr_init(pthread_attr_t *__attr);
```

线程属性包括：

1. 作用域；
2. 栈大小；
3. 栈地址；
4. 优先级；
5. 分离状态；
6. 调度策略；
7. 调度参数；

线程属性暂时不做深入研究

## 3.1、分离状态

线程终止时，系统将不再保留线程终止状态；当不需要线程的终止状态时，可以分离线程（调用 ***pthread_detach*** 函数），也可以通过设置线程的分离状态实现

```c
int pthread_attr_getdetachstate(const pthread_attr_t* attr, int* state);
int pthread_attr_setdetachstate(pthread_attr_t* attr, int state);
```

***state*** 的值可以是 **PTHREAD_CREATE_DETACHED** 和 **PTHREAD_CREATE_JOINABLE**，分别表示主线程阻塞和子线程剥离

## 3.2、线程优先级

新线程的优先级默认为0

```c
int  pthread_attr_getschedparam(const pthread_attr_t *restrict attr, struct sched_param *restrict param) ;
int pthread_attr_setschedparam(pthread_attr *restrict attr, const struct sched_param* restrict param);
```

## 3.3、继承父优先级

新线程不继承父线程的调度优先级

## 3.4、调度策略

线程使用 **SCHED_OTHER** 调度策略，线程一旦开始运行，直到被强占或者直到线程阻塞或者停止位置

```c
int pthread_attr_setschedpolicy(pthread_attr_t* attr, int policy);
int pthread_attr_setschedparam(pthread_attr_t* attr, struct sched_param* param) 
```

# 4、代码示例

```c
#include <iostream>
#include <pthread.h>
#include <unistd.h>

void *thread(void *arg){
    printf("thread id: %ld\n", pthread_self());
    int value = *(int *)arg;
    for (int index = 0; index < value; index++){
        printf("thread, arg, %d\n", index);
        sleep(1);
    }
    long res = 9;
    pthread_exit((void*)res);
    return arg;
}

int main(){
    void* res = 0;
    int value = 5;
    //设置线程属性
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);//线程分离

    pthread_t handle;
    pthread_create(&handle, &attr, thread, &value);
    //pthread_create(&handle, NULL, thread, &value);
    
    // pthread_detach(handle);//线程剥离
    pthread_join(handle, &res);//join阻塞
    printf("------end------,res: %ld\n", (long)res);//线程中传出的9
    return 0;
}
```



