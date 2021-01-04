# 1、说明

libuv 中实现 tcp server 的步骤和原生 socket 步骤类似，回忆一下 linux 下原生 socket 实现 tcp server 的步骤：

1. 初始化 socket 环境，获取 socket 套接字；
2. bind() 方法绑定套接字到本地IP；
3. listen() 方法监听 socket，获取新连接；
4. accept() 方法接受客户端连接，返回客户端套接字；
5. recv() 方法接受客户端的数据；
6. send() 方法向客户端发送数据；
7. closesocket() 方法关闭套接字；

libuv 和原生 socket 编程类似，步骤和API与原生 socket 变成步骤类似，但是使用却变得简单了，处处使用回调函数使得编程变得简单了。

# 2、libuv的tcp server

libuv 对于 tcp 消息的处理，同样是基于 stream 的，步骤如下：

1. uv_tcp_init() 建立 tcp 句柄；
2. uv_tcp_bind() 方法绑定ip；
3. uv_listen() 方法监听，有新连接时，调用回调函数；
4. uv_accept() 方法获取客户端套接字；
5. uv_read_start() 方法读取客户端数据；
6. uv_write() 方法想客户端发送数据；
7. uv_close() 关闭套接字；

# 3、API简介

附录是整个 tcp server 的源代码，其中涉及到的一些 API 如下：

## 3.1、uv_tcp_init

初始化 tcp 对象

```c
uv_tcp_t server;
uv_tcp_init(loop, &server);//初始化tcp server对象
```

## 3.2、uv_ip4_addr

```c
struct sockaddr_in addr;
uv_ip4_addr("0.0.0.0", DEFAULT_PORT, &addr);
```

将给定的ip地址和端口转换成sockaddr_in结构体，原生变成的时候，设置ip和端口需要至少五行，用这个方法可以简化操作

## 3.3、uv_tcp_bind

等同于原生API的 bind() 方法

```c
uv_tcp_bind(&server, (const struct sockaddr *) &addr, 0);
```

uv_tcp_bind() 的第三个参数 flag 一般是0，如果想使用IP6，可以使用 UV_TCP_IPV6ONLY

```c
enum uv_tcp_flags {
  /* Used with uv_tcp_bind, when an IPv6 address is used. */
  UV_TCP_IPV6ONLY = 1
};
```

## 3.4、uv_listen

```c
uv_listen((uv_stream_t *) &server, 128, on_new_connection);
```

类似 listen() ，开始监听

第二个参数表明内核的排队数，最后指定有新连接时的回调函数

当有新的连接进来时，就会触发 on_new_connection 回调

## 3.5、uv_connection_cb

**uv_connection_cb** 是 uv_listen 的回调函数，其声明如下：

```c
typedef void (*uv_connection_cb)(uv_stream_t* server, int status);
```

**server** 参数为服务器句柄

**status** 表示状态，小于0表示新连接有误

## 3.6、uv_accept

新连接触发回调函数之后，按照一般流程，需要使用 accept() 方法获取客户端句柄，libuv 中使用 uv_accept()，其声明如下：

```c
int uv_accept(uv_stream_t* server, uv_stream_t* client)
```

在调用之前，**client** 参数必须被初始化

返回值 <0 表示有误

**示例：**

```c
uv_tcp_t *client = (uv_tcp_t *) malloc(sizeof(uv_tcp_t));//为tcp client申请资源
uv_tcp_init(loop, client);//初始化tcp client句柄
if (uv_accept(server, (uv_stream_t *) client) == 0) {
	do_some_thind();
}
```

## 3.7、uv_read_start

libuv 中使用 uv_read_start() 方法从传入的 stream 中读取数据，声明如下：

```c
int uv_read_start(uv_stream_t* stream, uv_alloc_cb alloc_cb, uv_read_cb read_cb)
```

**read_cb** 会被多次调用，直到数据读完，或者主动调用 uv_read_stop() 方法停止

该函数有两个回调函数，**alloc_cb** 用于为新来的数据申请空间，申请的资源需要在 **read_cb** 中释放

这两个回调的声明如下：

```c
typedef void (*uv_alloc_cb)(uv_handle_t* handle, size_t suggested_size, uv_buf_t* buf);
```

```c
typedef void (*uv_read_cb)(uv_stream_t* stream, ssize_t nread, const uv_buf_t* buf);
```

**示例代码：**

```c
//负责为新来的消息申请空间
void alloc_buffer(uv_handle_t *handle, size_t suggested_size, uv_buf_t *buf) {
  buf->len = suggested_size;
  buf->base = static_cast<char *>(malloc(suggested_size));
}
/**
 * @brief: 负责处理新来的消息
 * @param: client
 * @param: nread>0表示有数据就绪，nread<0表示异常，nread是有可能为0的，但是这并不是异常或者结束
 */
void read_cb(uv_stream_t *client, ssize_t nread, const uv_buf_t *buf) {
	do_somt_thing();
    //释放之前申请的资源
  if (buf->base != NULL) {
    free(buf->base);
  }
}

uv_read_start((uv_stream_t *) client, alloc_buffer, read_cb);
```

## 3.8、uv_buf_t 和 uv_buf_init

**uv_buf_t** 是libuv 中的一种特殊的数据类型，和 Redis 的 SDS 有一点相似度，声明如下：

```c
typedef struct uv_buf_t {
  char* base;
  size_t len;
} uv_buf_t;
```

**uv_buf_t** 可以使用 **uv_buf_init** 初始化

**示例：**

```c
uv_buf_t uvBuf = uv_buf_init(buf->base, nread);//初始化write的uv_buf_t
```

## 3.9、uv_close

libuv 中使用 uv_close() 方法关闭句柄，声明如下：

```c
void uv_close(uv_handle_t* handle, uv_close_cb close_cb)
```

**close_cb** 为关闭之后的回调，声明如下：

```c
typedef void (*uv_close_cb)(uv_handle_t* handle);
```

**代码示例：**

```c
void on_close(uv_handle_t *handle) {
  if (handle != NULL)
    free(handle);
}
...
uv_close((uv_handle_t *) client, on_close);
```

## 3.10、uv_write

libuv 中使用 uv_write() 方法发送数据，声明如下：

```c
int uv_write(uv_write_t* req, uv_stream_t* handle, const uv_buf_t bufs[],
                       unsigned int nbufs, uv_write_cb cb);
```

**req** 是需要传递给回调函数的数据，发送需要申请资源，并在回调函数中释放

**handle** 是接受的客户端

**bufs[]** 是一个 **uv_buf_t** 数组，可以一次添加多组数据，最终按照顺序发送

**nbufs** 表示需要发送的数组元素个数，一般小于等于 bufs 的大小

## 3.11、uv_strerror

有些函数会有错误码，使用 uv_strerror() 方法获取错误码对应的描述

# 附录

源代码如下：

```c
#include <stdio.h>
#include <uv.h>
#include <stdlib.h>

uv_loop_t *loop;
#define DEFAULT_PORT 7000

//连接队列最大长度
#define DEFAULT_BACKLOG 128

//负责为新来的消息申请空间
void alloc_buffer(uv_handle_t *handle, size_t suggested_size, uv_buf_t *buf) {
  buf->len = suggested_size;
  buf->base = static_cast<char *>(malloc(suggested_size));
}

void on_close(uv_handle_t *handle) {
  if (handle != NULL)
    free(handle);
}

void echo_write(uv_write_t *req, int status) {
  if (status) {
    fprintf(stderr, "Write error %s\n", uv_strerror(status));
  }

  free(req);
}

/**
 * @brief: 负责处理新来的消息
 * @param: client
 * @param: nread>0表示有数据就绪，nread<0表示异常，nread是有可能为0的，但是这并不是异常或者结束
 * @author: sherlock
 */
void read_cb(uv_stream_t *client, ssize_t nread, const uv_buf_t *buf) {
  if (nread > 0) {
//    buf->base[nread] = 0;
    fprintf(stdout, "recv:%s\n", buf->base);
    fflush(stdout);

    uv_write_t* req = (uv_write_t*)malloc(sizeof(uv_write_t));

    uv_buf_t uvBuf = uv_buf_init(buf->base, nread);//初始化write的uv_buf_t

    //发送buffer数组，第四个参数表示数组大小
    uv_write(req, client, &uvBuf, 1, echo_write);

    return;
  } else if (nread < 0) {
    if (nread != UV_EOF) {
      fprintf(stderr, "Read error %s\n", uv_err_name(nread));
    } else {
      fprintf(stderr, "client disconnect\n");
    }
    uv_close((uv_handle_t *) client, on_close);
  }

  //释放之前申请的资源
  if (buf->base != NULL) {
    free(buf->base);
  }
}

/**
 *
 * @param:  server  libuv的tcp server对象
 * @param:  status  状态，小于0表示新连接有误
 * @author: sherlock
 */
void on_new_connection(uv_stream_t *server, int status) {
  if (status < 0) {
    fprintf(stderr, "New connection error %s\n", uv_strerror(status));
    return;
  }

  uv_tcp_t *client = (uv_tcp_t *) malloc(sizeof(uv_tcp_t));//为tcp client申请资源

  uv_tcp_init(loop, client);//初始化tcp client句柄

  //判断accept是否成功
  if (uv_accept(server, (uv_stream_t *) client) == 0) {
    //从传入的stream中读取数据，read_cb会被多次调用，直到数据读完，或者主动调用uv_read_stop方法停止
    uv_read_start((uv_stream_t *) client, alloc_buffer, read_cb);
  } else {
    uv_close((uv_handle_t *) client, NULL);
  }
}

int main(int argc, char **argv) {
  loop = uv_default_loop();

  uv_tcp_t server;
  uv_tcp_init(loop, &server);//初始化tcp server对象

  struct sockaddr_in addr;

  uv_ip4_addr("0.0.0.0", DEFAULT_PORT, &addr);//将ip和port数据填充到sockaddr_in结构体中

  uv_tcp_bind(&server, (const struct sockaddr *) &addr, 0);//bind

  int r = uv_listen((uv_stream_t * ) & server, DEFAULT_BACKLOG, on_new_connection);//listen

  if (r) {
    fprintf(stderr, "Listen error %s\n", uv_strerror(r));
    return 1;
  }

  return uv_run(loop, UV_RUN_DEFAULT);
}
```

