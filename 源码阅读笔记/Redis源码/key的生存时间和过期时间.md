# 1、说明

**生存时间：** （Time To Live, TTL），经过指定的秒/毫秒之后，服务器自动删除TTL为0的key

**过期时间：** （expire time），时间戳，表示一个具体时间点，到这个时间点后，服务器会删除key

# 2、指令

**设置生存时间：**

```python
EXPIRE key ttl	#设置ttl，s
PEXPIRE key ttl	#设置ttl，ms
```

**设置过期时间：**

```python
EXPIREAT key timestamp	#设置expire time，s
PEXPIREAT key timestamp	#设置exprie time，ms
```

以上四种命令虽然各有不同，但是其底层都是使用 **PEXPIREAT** 实现的

## 2.1、删除和更新

```python
PERSIST key	#移除生存时间
```

**DLE** 命令可以删除key，也会删除其生存时间

**SET** 和 **GETSET** 命令也可以覆写生存时间

# 3、过期时间的保存

redisDb结果的expires字典中保存了数据库中的所有key的过期时间，redisDb的声明如下：

```c
/* Redis database representation. There are multiple databases identified
 * by integers from 0 (the default database) up to the max configured
 * database. The database number is the 'id' field in the structure. */
//每个数据库都是一个redisDb，id为数据库编号
typedef struct redisDb {
    dict *dict;   	//键空间，保存了数据中所有键值对
    dict *expires; 	//过期字典，保存了数据库中所有键的过期时间
    dict *blocking_keys;
    dict *ready_keys;
    dict *watched_keys;
    struct evictionPoolEntry *eviction_pool;
    int id;               	/* Database ID */
    long long avg_ttl;     	/* Average TTL, just for stats */
} redisDb;
```

**expires** 的键是一个指针，指向某个键对象，值是一个 long long 类型整数，保存了过期时间，是一个毫秒精度的UNIX时间戳

可见，过期时间的保存是使用key来作为关联的，所以操作用，修改key均可以修改过期时间，而只修改key的value，是不是改变其过期时间的

# 4、计算剩余生存时间

```python
TTL key #计算key的剩余生存时间，s
PTTL key #计算key的剩余生存时间，ms
```

底层的处理方式也很简单，获取key的生存时间戳，减去当前时间戳即可

如果键不存在，则返回-2

如果键没有设置过期时间，则返回-1

同样可以使用此方法判断key是否过期，TTL/PTTL 结果小于0，则表示过去，大于0，则表示未过期

# 5、过期键的删除策略

策略有三种：

1. 定时删除：设置键的过期时间的同时，设置一个定时器，来删除键
2. 惰性删除：放任过期键不管，每次从键空间取值时，检查是否过期，以决定是否删除；
3. 定期删除：每个一段时间，进行一次数据库检查，删除里面的过期键，至于，要删除多少过期键，以及要检查多少数据库，由算法决定；

## 5.1、定时删除

对内存友好，保证键尽可能在失效时立即删除；

对CPU不友好，过期键比较多时，删除可能会占用一部分CPU时间，影响，服务器的响应时间和吞吐量；

Redis 中创建定时器需要使用Redis的时间事件（实现方式是无序链表，查找的时间复杂度为O(N)），大量事件处理时效率太低

## 5.2、惰性删除

对CPU最友好，只有在取键的值时才处理；

对内存不友好，过期键如果长时间没有被操作，则过期键仍然会占用内存，大量的无用数据占用内存，可以认为是一种内存泄漏；

Redis 的惰性删除策略由 db.c/expireIfNeeded 函数实现，所有读写Redis的命令在执行之前都会调用expireIfNeeded 函数

## 5.3、定期删除

定期删除是定时删除和惰性删除的一种折中方式，其难点是确定删除操作执行的时长和频率

Redis 的定期删除策略由 db.c/activeExpireCycle 函数实现，每当 Reids 周期性操作 redis.c/serverCron 函数执行时，activeExpireCycle 函数就会被调用，在规定时间内，多次遍历服务器中的各个数据库，随机检查一部分键的过期时间，删除过期键