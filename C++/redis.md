# redis

关于redis，以及其如何安装使用，这里不做介绍，毕竟本文的主要内容是C语言的的redis库-hiredis

# hiredis

***hiredis*** 是一个使用C语言封装的一组库函数，用于连接 ***redis***，其内部提供了一些基本的接口，用于连接、执行等操作。其是官方提供的库，下载 ***redis*** 源码的时候会自带 ***hiredis***

***hiredis*** 的构建方式在windows和linux上有所不同，下面分别来介绍 ***hiredis*** 的使用方式

## Linux

**操作系统：** ubuntu 16.04

**IDE：** qtcreator/g++

下载 ***hiredis*** 源码，github地址：https://github.com/redis/hiredis

编译安装 ***hiredis***

```
make
make install
```

一起OK，下面编写测试代码

新建工程，按需.h/.c文件添加到项目中

```c++
int main(int argc, char *argv[])
{
    redisContext* redisContext = redisConnect("192.168.125.128", 6379);
    if(redisContext->err)
    {
        redisFree(redisContext);
        cout<<"connect redis server fail:"<<redisContext->errstr<<endl;
        return 1;
    }
    cout<<"connect redis server pass"<<endl;
    string cmd = "AUTH " + password;
    redisReply* reply = (redisReply*)redisCommand(redisContext, cmd.c_str());
    cout<<reply->str<<endl;
    
    cmd = "PING";
    reply = (redisReply*)redisCommand(redisContext, cmd.c_str());
    cout<<reply->str<<endl;

    freeReplyObject(reply);

    return 0;
}
```

**代码说明：**

1. ***hiredis*** 的操作分为异步和同步，此示例代码是同步操作

2. 同步操作常用的API主要有三个：

   ```c++
   redisContext *redisConnect(const char *ip, int port);	//连接redis
   void *redisCommand(redisContext *c, const char *format, ...);//执行指令
   void freeReplyObject(void *reply);	//释放连接
   ```

   异步操作的主要API：

   ```c++
   redisAsyncContext *redisAsyncConnect(const char *ip, int port)；
   int redisAsyncCommand(redisAsyncContext *ac, redisCallbackFn *fn, 
                         void *privdata, const char *format, ...)；
   void redisAsyncDisconnect(redisAsyncContext *ac)；
   ```

## windows

windows的 ***hiredis*** 和linux上的不一样，下载地址：https://github.com/microsoftarchive/redis

需要编译的是目录下的 **msvs** 目录下的源码，源码包括了 **redis** 等，不需要全部编译

这里需要编译 **hiredis** 和 **Win32_Interop** 项目，项目默认是x64位的，编译完成之后生成两个.lib文件，就是自己的项目需要的，将这两个.lib添加到自己的工程中

头文件使用源码目录下的 **deps/hiredis** 和 **src/Win32_Interop** 目录下的文件，将这两个文件加copy到自己工程的目录下，并添加库文件路径，注意这两个文件夹的相对路径不要改变

需要注意的是，编译的时候，是64位，则调用的项目也要是64位，不然会链接错误，当然，如果要使用32位的，源码也要用32位的编译

**示例代码：**

API的使用和linux下相同，这里不再复制一遍

编译可能会有错误：

1. **值“MTd_StaticDebug”不匹配值**，则将项目的运行库改成 **多线程（/MT）** 即可；
2. **unsafe** 的报错/警告，可以在项目配置中忽略；
3. **libcpmtd.lib** 的库冲突，则在项目配置的链接器配置的命令行中添加 **/NODEFAULTLIB:libcmt.lib** 即可；





