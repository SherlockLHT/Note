# 1、uv_timer_t - 计时器句柄

使用该类型句柄来调用计时器回调

# 2、API

## 2.1、uv_timer_init

```c
int uv_timer_init(uv_loop_t* loop, uv_timer_t* handle)
```

初始化计时器句柄

## 2.2、uv_timer_start

```c
int uv_timer_start(uv_timer_t* handle, uv_timer_cb cb, uint64_t timeout, uint64_t repeat)
```

```c
void (*uv_timer_cb)(uv_timer_t* handle)
```

开始计时器，***timeout*** 和 ***repeat*** 都是以 ***ms*** 作为单位

如果 ***timeout*** 为 0，则回调函数在下次时间迭代中执行，如果非0，则回调函数会在 ***timeout*** 后第一次执行，接下来每次在 ***repeat*** 后执行

## 2.3、uv_timer_stop

```c
int uv_timer_stop(uv_timer_t* handle)
```

停止计时器，回调函数不会再执行

## 2.4、uv_timer_again

```c
int uv_timer_again(uv_timer_t* handle)
```

停止计时器，如果计时器已经开始并正在正在重复执行，则使用 ***repeat*** 作为 ***timeout*** 重启，若计时器还没有开始，则返回 ***UV_EINVAL***

## 2.5、uv_timer_set_repeat

```c
void uv_timer_set_repeat(uv_timer_t* handle, uint64_t repeat)
```

设置计时器的 ***repeat*** 值，计时器会根据改值进行调度

如果 ***repeat*** 为50ms，首次运行了17ms，则33ms后会再次运行。而如果有其他任务占用，超出了33ms，则之后回调函数会尽快去执行

## 2.6、uv_timer_get_repeat

```c
uint64_t uv_timer_get_repeat(const uv_timer_t* handle)
```

返回 ***repeat*** 值

## 2.7、uv_timer_get_due_in

```c
uint64_t uv_timer_get_due_in(const uv_timer_t* handle)
```

获取计时器的到期值，这个值是相对于 ***uv_now()*** 的

如果计时器失效，则返回0

# 3、代码示例

```c
void timerCB(uv_timer_t *handle) {
  auto value = (long long) handle->data;
  cout << value << endl;
  if (value >= 126) {
    uv_timer_stop(handle);
    return;
  }
  handle->data = (void *) ++value;
}

int main() {

  uv_loop_t *loop = uv_default_loop();

  uv_timer_t timer;
  uv_timer_init(loop, &timer);

  timer.data = (void *) 123;
  uv_timer_start(&timer, timerCB, 1000, 4000);

  uv_run(loop, UV_RUN_DEFAULT);

  return 0;
}
```

