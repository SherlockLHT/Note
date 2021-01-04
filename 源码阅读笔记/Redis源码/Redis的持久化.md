# 1、持久化的两种方式

Redis 持久化方式有两种：RDB 和 AOF

## 1.1、RDB

**执行指令：**

```python
SAVE
BGSAVE
```

上面两个指令都可以让 Redis 创建一个 RDB 文件，保存键数据，如果 RDB 文件存在，则会覆盖

区别是 SAVE 指令会阻塞 Redis 的服务进程，直到 RDB 文件创建完成，阻塞期间，Redis 无法处理任何命令。BGSAVE（BG则是background的意思） 则会 fork() 一个子进程来进程来进行 RDB 文件的创建，因此不会阻塞 Redis 的服务进程，创建完成之后，子进程向父进程发送信号，告知创建完成。

RDB 文件名可以由配置文件指定中的 dbfilename 值指定，默认是 dump.rdb

RDB 文件的创建由 rdb.c/rdbSave 函数完成



