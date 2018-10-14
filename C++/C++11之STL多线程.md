- STL库跨平台；
- VS2010不支持`std::thread`库，至少VS2012/2013及其以上可以；

### 一、库概要

##### （1）`std::thread`成员函数

```c++
thread(fun, args...);	//构造函数，传入函数，后面跟参数，若是类普通成员，需要加this指针作为参数1
void swap(thread& other);	//线程交换
bool joinable() const;		//是否可以加入
void join();				//加入线程，开始线程，当前线程阻塞
void detach();				//分离线程，开始线程，当前线程不阻塞
std::thread::id get_id();	//获取线程id
native_handle_type native_handle();	//获取线程句柄
static unsigned int hardware_concurrency();	//检测硬件并发特性，即最多运行的线程数目
```

==当线程部阻塞运行时，主进程退出而子线程还在运行，则子线程不会退出，变成孤儿线程。==

==孤儿线程不会造成什么危害，操作系统会对其进行处理，但应尽量避免。==

##### （2）线程中获取线程id

- 使用`this_thread`命名空间；
- 

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

```c++
this_thread.sleep_for(chrono::second(2)); //延时2s
this_thread::sleep_for(chrono::milliseconds(5000));	//延时5000ms
```

