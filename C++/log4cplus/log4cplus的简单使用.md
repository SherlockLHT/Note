# 一、前言

一直以来，我使用c++开发的时候的日志都使用Qt的QDebug模块导向文件，虽然中间自己改过源码，实现了向下兼容的分模块保存日志的功能，但是这都是简单浅显的日志功能。

而对于大型系统的开发，自己写的简单的类库就显得心有余而力不足了，所以现在开始使用一些著名的日志系统。本篇主要介绍 **log4cplus** 的简单使用。

下面的链接中使用趣味的故事介绍了log4j的设计方法，而 **log4cplus** 则是log4j在c++上的移植版，接口和功能基本相同。

[一个著名的日志系统如何设计出来的？](https://mp.weixin.qq.com/s/XiCky-Z8-n4vqItJVHjDIg)

**log4cplus** API文档：[log4cplus Documentation](https://log4cplus.sourceforge.io/docs/html/index.html)

# 二、log4cplus的基本类

| 类     | 说明 |
| ------ | ---- |
| Logger | 日志记录句柄 |
| Appender | 日志的输出源，表示该日志需要输出到哪里，可以添加多个 |
| Layout | 日志输出格式，一个Appender一个Layout |

**Logger：** 句柄，用于获取记录日志的实例

**Appender：** 顾名思义，输出源，它可以配置接下来的日志内容输出到哪里，比如，输出到console、文件、远程的log服务器等其中一个或者多个

**Layout：** 用于表示记录的日志的格式，一个Appender一个Layout，但是若是输出到远程的log服务器则不需要Layout

所以，使用的步骤大致如下：

1. 初始化 **log4cplus**；
2. 创建 **Appender** 对象；
3. 设置 **Appender** 对象的名称和输出的 **Layout**；
4. 使用 **Logger** 实例添加 **Appender**；
5. 使用宏定义输出日志；

# 三、支持的Appender和Layout

log4cplus支持的输出源很多，每种Appender都继承自Appender，类图如下所示：

![1575902570411](1575902570411.png)

支持的Layout如下：

![1575902659579](1575902659579.png)

# 四、代码示例

## （1）输出日志到console

```c++
int main() {
	//初始化
	Initializer initializer;
	//创建指向console的appender
	SharedAppenderPtr appender(new ConsoleAppender());
	//设置appender的layout
	appender->setName(LOG4CPLUS_TEXT("console"));
	appender->setLayout(std::unique_ptr<Layout>(new SimpleLayout));

	//获取logger实例
	Logger logger = Logger::getInstance(LOG4CPLUS_TEXT("test"));
	//设置这个logger实例的log输出等级
	logger.setLogLevel(INFO_LOG_LEVEL);
	//添加appender
	logger.addAppender(appender);
	//利用宏定义输出日志
	LOG4CPLUS_INFO(logger, LOG4CPLUS_TEXT("test log中文测试"));

	return 0;
}
```

## （2）输出日志到文件

```c++
int main() {
	//初始化
	Initializer initializer;

	//创建指向文件的appender
	tstring fileName = LOG4CPLUS_TEXT("mylog.log");
	SharedAppenderPtr fileAppender(new FileAppender(fileName, std::ios_base::app));
	fileAppender->setName(LOG4CPLUS_TEXT("file"));
	tstring pattern = LOG4CPLUS_TEXT("%D{%y-%m-%d %H:%M:%S,%Q} [%t] %-5p %c - %m [%l]%n");
	fileAppender->setLayout(std::unique_ptr<Layout>(new PatternLayout(pattern)));

	//获取logger实例
	Logger logger = Logger::getInstance(LOG4CPLUS_TEXT("test"));
	//设置这个logger实例的log输出等级
	logger.setLogLevel(INFO_LOG_LEVEL);
	//添加appender
	logger.addAppender(fileAppender);
	//利用宏定义输出日志
	LOG4CPLUS_INFO(logger, LOG4CPLUS_TEXT("test log"));

	return 0;
}
```

## （3）输出日志到日志服务器

```c++
int main() {
	//初始化
	Initializer initializer;
	//创建日志服务器appender
	SharedAppenderPtr socketAppender(
        new SocketAppender(LOG4CPLUS_TEXT("127.0.0.1"), 8888, LOG4CPLUS_TEXT("test"))
    );
	socketAppender->setName(LOG4CPLUS_TEXT("logserver"));

	//获取logger实例
	Logger logger = Logger::getInstance(LOG4CPLUS_TEXT("test"));
	//设置这个logger实例的log输出等级
	logger.setLogLevel(INFO_LOG_LEVEL);
	//添加appender
	logger.addAppender(socketAppender);
	//利用宏定义输出日志
	LOG4CPLUS_INFO(logger, LOG4CPLUS_TEXT("test log中文测试"));

	return 0;
}
```

日志服务器的示例，参考示例代码 **loggingserver.cxx**