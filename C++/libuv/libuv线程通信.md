# 1、说明

用于多线程之间传递参数

# 2、API

## 2.1、uv_async_init

```c
int uv_async_init(uv_loop_t* loop, uv_async_t* async, uv_async_cb async_cb);
```

初始化句柄（***uv_async_t*** 类型），回调函数 ***async_cb*** 可以为NULL

返回0表示成功，<0 表示错误码

## 2.2、uv_async_send

```c
int uv_async_send(uv_async_t* async);
```

唤醒时间循环，执行 ***async*** 的回调函数（***uv_async_init*** 初始化指定的回调）

***async*** 将被传递给回调函数

返回0表示成功，<0 表示错误码

在任何线程中调用此方法都是安全的，回调函数将会在 ***uv_async_init*** 指定的 ***loop*** 线程中执行

## 2.3、uv_close

```c
void uv_close(uv_handle_t* handle, uv_close_cb close_cb)
```

和 ***uv_async_init*** 对应，调用之后执行回调 ***close_cb***

***handle*** 会被立即释放，但是 ***close_cb*** 会在事件循环到来之时执行，用于释放句柄相关的其他资源

# 3、代码示例

```c
#include <iostream>
#include <uv.h>
#include <stdio.h>
#include <unistd.h>

uv_loop_t *loop;
uv_async_t async;

double percentage;

void print(uv_async_t *handle)
{
    printf("thread id: %ld, value is %ld\n", uv_thread_self(), (long)handle->data);
}

void run(uv_work_t *req)
{
    long count = (long)req->data;
    for (int index = 0; index < count; index++)
    {
        printf("run thread id: %ld, index: %d\n", uv_thread_self(), index);
        async.data = (void *)(long)index;
        uv_async_send(&async);
        sleep(1);
    }
}

void after(uv_work_t *req, int status)
{
    printf("done, thread id: %ld\n", uv_thread_self());
    uv_close((uv_handle_t *)&async, NULL);
}

int main()
{
    printf("main thread id: %ld\n", uv_thread_self());
    loop = uv_default_loop();

    uv_work_t req;
    int size = 5;
    req.data = (void *)(long)size;

    uv_async_init(loop, &async, print);
    uv_queue_work(loop, &req, run, after);

    return uv_run(loop, UV_RUN_DEFAULT);
}
```

示例中，***print()*** 函数将会在 ***loop*** 所在的线程中执行