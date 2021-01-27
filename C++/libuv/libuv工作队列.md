# 1、说明

libuv 中的工作队列中的任务会在线程池中执行

# 2、API

## 2.1、uv_queue_work

```c
int uv_queue_work(uv_loop_t* loop, 
                  uv_work_t* req, 
                  uv_work_cb work_cb, 
                  uv_after_work_cb after_work_cb);
```

添加一个任务到工作队列中，在主线程中调用

***loop***： 事件循环

***req***： 传入到任务的数据，一般使用 req.data 参数传递

***work_cb***： 执行方法

***after_work_cb***： 执行方法完成后执行

***work_cb*** 方法会在函数中执行，***after_work_cb*** 方法在创建线程中执行

## 2.2、uv_cancel

```c
int uv_cancel(uv_req_t* req);
```

取消未执行的队列中的任务，在任务中调用

***req*** 为任务的参数

如果调用此方法取消了任务，则 ***after_work_cb*** 回调函数的 ***status*** 的值为 ***UV_ECANCELED***；

# 3、代码示例

```c
#include <iostream>
#include <pthread.h>
#include <unistd.h>
#include <uv.h>

void print(uv_work_t *req)
{
    sleep(1);
    long num = (long)req->data;
    printf("thread id is: %ld, num is: %d\n", uv_thread_self(), num);
}

void after_print(uv_work_t *req, int status)
{
    printf("after print, req data is %d, status is %d\n", req->data, status);
}

int main()
{
    uv_loop_t *loop = uv_default_loop();
    uv_work_t req[5];

    for (int index = 0; index < 5; index++)
    {
        req[index].data = (void *)(long)index;
        uv_queue_work(loop, &req[index], print, after_print);
        sleep(1);
    }

    return uv_run(loop, UV_RUN_DEFAULT);
}
```

示例中的代码，每次执行 ***print()*** 方法都是在不同线程中，***after_print()*** 方法和 ***main()*** 方法在同一个线程中