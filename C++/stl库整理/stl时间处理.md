# 说明

习惯使用Qt的QDateTime、QDate 和 QTime 进行时间处理，脱离Qt，标准C++用起来虽然不是太习惯，但是可以避免引入Qt库

下面介绍几种标准C++处理日期时间的方法，考虑平台的通用性，暂时不关注平台API

# 1、std::time及其格式化

**头文件：** #include \<ctime\>

**声明：**

```c++
/* Return the current time and put it in *TIMER if TIMER is not NULL. */
extern time_t time (time_t *__timer) __THROW;
```

**说明：**

如注释所说，返回当前的时间，如果传入的time_t不是空的话，同时再赋值给该参数

**time_t** 通过层层定义，可以看到是 **long int** 型的数据结构

这里的结果是一个距离UTC时间（1970年1月1日0时0分0秒）的秒数

## 1.1、localtime/gmtime

**头文件：** #include <time.h>

**声明：**

```c++
/* Return the `struct tm' representation of *TIMER
   in Universal Coordinated Time (aka Greenwich Mean Time).  */
extern struct tm *gmtime (const time_t *__timer) __THROW;

/* Return the `struct tm' representation
   of *TIMER in the local timezone.  */
extern struct tm *localtime (const time_t *__timer) __THROW;
```
struct tm是标准C++的一种事件处理结构体，声明如下：
```c++
/* ISO C `broken-down time' structure.  */
struct tm
{
  int tm_sec;			/* Seconds.	[0-60] (1 leap second) */
  int tm_min;			/* Minutes.	[0-59] */
  int tm_hour;			/* Hours.	[0-23] */
  int tm_mday;			/* Day.		[1-31] */
  int tm_mon;			/* Month.	[0-11] */
  int tm_year;			/* Year	- 1900.  */
  int tm_wday;			/* Day of week.	[0-6] */
  int tm_yday;			/* Days in year.[0-365]	*/
  int tm_isdst;			/* DST.		[-1/0/1]*/

# ifdef	__USE_MISC
  long int tm_gmtoff;		/* Seconds east of UTC.  */
  const char *tm_zone;		/* Timezone abbreviation.  */
# else
  long int __tm_gmtoff;		/* Seconds east of UTC.  */
  const char *__tm_zone;	/* Timezone abbreviation.  */
# endif
};
```

**说明：**

gmtime 和 localtime 的区别在于，localtime 算进当地的时区，gmtime 算的是格林威治时间，即0时区，以北京时间来算，localtime 会比 gmtime 快8小时

**示例代码：**

```c++
time_t res = time(nullptr);
tm* tm = localtime(&res);
```



# 2、std::chrono的时钟Clock

参考：https://www.cnblogs.com/zhongpan/p/7490657.html

先吐槽一句，stl的源码写的，真的不是给普通人读的，太晦涩难懂了

**std::chrono** 用于高精度时间转换，先复习一下时间单位

1s = 1,000ms = 1,000,000us = 1,000,000,000ns = 1,000,000,000,000ps

**std::chrono** 有三种时钟处理，分别是：

- system_clock；
- steady_clock；
- high_resolution_clock；

这三种方式获取的时间都是 **纳秒** 的单位

## 2.1、system_clock

**system_clock** 用于获取系统时间，能够获取到从1970年1月1日0时0分0秒距今的纳秒数

它有方法 **to_time_t()**  可以转换成 time_t

受系统时间修改影响
**示例：**

```c++
chrono::system_clock::time_point time = chrono::system_clock::now();
time_t second = chrono::system_clock::to_time_t(time);
char timeFormat[32] = {0x00};
strftime(timeFormat, sizeof(timeFormat), "%Y-%m-%d %H:%M:%S", localtime(&second));
cout << timeFormat << endl;	//2020-10-11 20:44:20
```

## 2.2、steady_clock

和 **system_clock** 的区别在于，它计算的时间不是UTC，而是从系统开启到现在的纳秒数

它没有 **to_time_t()** 方法

它不受系统时间修改的影响

**示例：**

```c++
chrono::steady_clock::time_point timePoint = chrono::steady_clock::now();
cout<<timePoint.time_since_epoch().count()<<endl;//19440549678300
```

## 2.3、high_resolution_clock

和 **steady_clock** 相同

# 3、时间格式化

## 3.2、ctime和asctime

日期数据格式化，但是是 **Day Mon dd hh:mm:ss yyyy** 格式

## 3.3、strftime和strptime

**头文件：** #include <time.h>

**声明：**

```c++
/* Format TP into S according to FORMAT.
   Write no more than MAXSIZE characters and return the number
   of characters written, or 0 if it would exceed MAXSIZE.  */
extern size_t strftime (char *__restrict __s, size_t __maxsize,
			const char *__restrict __format,
			const struct tm *__restrict __tp) __THROW;

/* Parse S according to FORMAT and store binary time information in TP.
   The return value is a pointer to the first unparsed character in S.  */
extern char *strptime (const char *__restrict __s,
		       const char *__restrict __fmt, struct tm *__tp)
     __THROW;
```

这两种都是将标准C++的处理时间的结构体 **tm** 格式化的函数，区别和标准C函数一样，strftime 函数多了一个 size_t 参数，会更加安全

可以使用的格式化字符串如下：

```
%a 星期几的简写 
%A 星期几的全称 
%b 月分的简写 
%B 月份的全称 
%c 标准的日期的时间串 
%C 年份的后两位数字 
%d 十进制表示的每月的第几天 
%D 月/天/年 
%e 在两字符域中，十进制表示的每月的第几天 
%F 年-月-日 
%g 年份的后两位数字，使用基于周的年 
%G 年分，使用基于周的年 
%h 简写的月份名 
%H 24小时制的小时 
%I 12小时制的小时
%j 十进制表示的每年的第几天 
%m 十进制表示的月份 
%M 十时制表示的分钟数 
%n 新行符 
%p 本地的AM或PM的等价显示 
%r 12小时的时间 
%R 显示小时和分钟：hh:mm 
%S 十进制的秒数 
%t 水平制表符 
%T 显示时分秒：hh:mm:ss 
%u 每周的第几天，星期一为第一天 （值从0到6，星期一为0）
%U 第年的第几周，把星期日做为第一天（值从0到53）
%V 每年的第几周，使用基于周的年 
%w 十进制表示的星期几（值从0到6，星期天为0）
%W 每年的第几周，把星期一做为第一天（值从0到53） 
%x 标准的日期串 
%X 标准的时间串 
%y 不带世纪的十进制年份（值从0到99）
%Y 带世纪部分的十进制年份 
%z，%Z 时区名称，如果不能得到时区名称则返回空字符。
%% 百分号
```

## 3.4、示例代码

```c++
time_t res = time(nullptr);
tm* tm = localtime(&res);

char timeFormat[32] = {0x00};
strftime(timeFormat, sizeof(timeFormat), "%Y-%m-%d %H:%M:%S", tm);
cout<<timeFormat<<endl;
```

输出

```
linux
2020-10-11 18:59:53
```

# 4、this_thread::sleep_until

**this_thread::sleep_until** ，顾名思义，线程阻塞，知道某个时候，这里传入的 chrono::system_clock::time_point

**示例：**

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

