- STL库跨平台；
- VS2010不支持`std::thread`库，至少VS2012/2013及其以上可以；

### 一、库概要

##### （1）`std::thread`成员函数

```c++
thread(fun, args...);	//构造函数，传入函数，后面跟参数，若是类普通成员，需要加this指针作为参数1
void swap(thread& other);	//线程交换
bool joinable() const;		//是否可以加入
void join();				//线程加入线程
void detach();				//线程分离
std::thread::id get_id();	//获取线程id
native_handle_type native_handle();	//获取线程句柄
static unsigned int hardware_concurrency();	//检测硬件并发特性，即最多运行的线程数目
```

==当线程部阻塞运行时，主进程退出而子线程还在运行，则子线程不会退出，变成孤儿线程。==

==孤儿线程不会造成什么危害，操作系统会对其进行处理，但应尽量避免。==

##### （2）线程中获取线程id

- 使用`this_thread`命名空间；

```c++
this_thread::get_id();	//获取当前线程的线程id
this_thread::yield();
this_thread::sleep_for();
this_thread::sleep_until();
```



### 二、应用示例

##### （1）创建线程

```c++
void fun_1()
{
	while (true)
	{
		cout << "fun_1:" << this_thread::get_id() << endl;
	}
}
void fun_2()
{
	while (true)
	{
		cout << "fun_2:" << this_thread::get_id() << endl;
	}
	
}
int main()
{
    thread t_1(fun_1);
	t_1.detach();

	thread t_2(fun_2);
	t_2.detach();
    
    return 0;
}
```

##### （2）类成员作为线程入口 -- 类中创建线程

- 参数1必须要传，表示当前对象，后面参数表示函数参数；

```c++
class TestClass
{
public:
	void fun_1_thread_enter()
	{
		thread t_1(&TestClass::fun1, this, 123);
		t_1.detach();
	}
	void fun_2_thread_enter()
	{
		thread t_2(&TestClass::fun2, this, 456);
		t_2.detach();
	}
private:
	void fun1(int temp)
	{ 
		while (true)
		{
			cout << "TestClass::fun_1" << this_thread::get_id() << temp << endl;
		}
	};
	void fun2(int temp)
	{
		while (true)
		{
			cout << "TestClass::fun_2" << this_thread::get_id() << temp << endl;
		}
	};
};

int main()
{
	TestClass test_class;
	test_class.fun_1_thread_enter();
	test_class.fun_2_thread_enter();

	while (true)
	{
		cout << "Main thread id:" << this_thread::get_id();
	}
	return 0;
}
```

##### （3）获取CPU最高并发数目

```c++
unsigned int num = thread::hardware_concurrency();
```

##### （4）线程移动

- 下例，通过`move`函数移动线程1到线程2，线程2获得线程1的所有属性；

```c++
int main()
{
	thread t_1(fun_1);
	cout << "Thread 1 id:" << t_1.get_id() << endl;

	thread t_2 = move(t_1);
	cout << "Thread 2 id:" << t_2.get_id() << endl;

	return 0;
}
```

##### （5）延时函数

- 使用`this_thread`命名空间中的`sleep_for`或者`sleep_until`函数实现等待；
- `std::chrono`是一个时间库；

**sleep_for** 用于阻塞一段时间

```c++
this_thread.sleep_for(chrono::second(2)); //延时2s
this_thread::sleep_for(chrono::milliseconds(5000));	//延时5000ms
```
**sleep_until** 传入一个具体时间，阻塞知道指定的时间

```c++
void printCurrentTime(tm* tm){
    char timeFormat[32] = {0x00};
    strftime(timeFormat, sizeof(timeFormat), "%Y-%m-%d %H:%M:%S", tm);
    cout<<timeFormat<<endl;
}
```

```c++
time_t res = time(nullptr);
tm* tm = localtime(&res);

printCurrentTime(tm);
tm->tm_min += 1;
this_thread::sleep_until(chrono::system_clock::from_time_t(mktime(tm)));
printCurrentTime(tm);
```

```c++
2020-10-11 21:05:06	//阻塞前
2020-10-11 21:06:06	//阻塞后
```

### 三、`join`和`detach`

***该项为新增项，在发现上文的错误后，新增第三项，而出现错误的原因是，上文参考了很多博客的文章，不是说所有博客的文章都是错的，搜出来的文章很多都是重复或者转载的，第一个写相关文章的人也许是对的，但是随着后来的人，尤其是一知半解的人，再加上自己的理解，难免出现一些偏差，要不是和同事讨论这个问题，也许我都不会发现这个错误。记录下来，作为教训。***

上文在修改前有一个错误，`join()`和`detach()`方法是对线程进行操作，不是开始线程，而***在初始化结束后，线程已经开始***。

看下这两个函数的官方解释：

> ```c++
> void join();
> ```
>
> **Join thread**
>
> The function returns when the thread execution has completed.
> This synchronizes the moment this function returns with the completion of all the operations in the thread: This blocks the execution of the thread that calls this function until the function called on construction returns (if it hasn't yet).

**可见，是说，这个函数知道线程执行完成后才返回，即当调用`join()`方法时，若线程函数正在执行，则阻塞，若线程函数已经执行完成，则返回。**

>```c++
>void detach();
>```
>
>**Detach thread**
>
>Detaches the thread represented by the object from the calling thread, allowing them to execute independently from each other.
>
>Both threads continue without blocking nor synchronizing in any way. Note that when either one ends execution, its resources are released.

**将该线程从创建线程中分离开，让这两个线程各自独立的运行，且各自线程的资源由各自释放。**

综上所述，`join`和`detach`方法时对线程进行操作，而不是作为线程的开始标志，当申请`std::thread`资源结束的时候，线程已经开始执行。

#### 那么`join`还好说，会阻塞主线程，那么`detach`方法的意义在哪？

如官方说明，这两个线程会在执行结束后各自释放各自的资源，即`detach`的意义在于资源上。

- 若并行且不调用`detach`，则子线程并未从主线程分离，主线程结束后，子线程的资源得不到释放，造成资源泄漏；
- 使用`detach`方法，子线程从主线程分离，主线程结束不会关心子线程的状况，子线程结束后，释放子线程的资源；

当然，调用`detach`方法也有风险，因为子线程从主线程中分离了，若整个程序的主线程都结束而其创建的子线程还在运行，则子线程就变成了孤儿线程。如何避免孤儿线程，相信这不是一个很困难的问题吧。