# 1、说明

当我们使用 Redis 的 Hash 操作时，底层的实现就是字典。

在介绍字典之后，我们先回忆一下 Redis 中的 Hash 操作。最常用的就是 **HSET** 和 **HGET** 了

```
127.0.0.1:6379> HSET user name sherlock
(integer) 1
127.0.0.1:6379> HSET user age 20
(integer) 1
127.0.0.1:6379> HGET user name
"sherlock"
127.0.0.1:6379> HGET user age
"20"
127.0.0.1:6379>
```

除了 **HSET** 和 **HGET** 外的常见指令还有：**HDEL、HEXISTS、HGETALL、HMGET 等等**，这里就不一一列举了，Redis 的 Hash 操作一般都是以 H 开头的。

我们可以看到，Hash 操作可以保存很多组键值对，其底层的视线就是字典

# 2、dict

字典的定义在源码目录下 src/dict.h 文件中，为了便于理解，我们从最基本的结构往上介绍

## 2.1、dictEntry

首先是 **dictEntry**，它表示字典中的一组键值对，声明如下：

```c
typedef struct dictEntry {
    void *key;	//键
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
        double d;
    } v;	//值
    struct dictEntry *next;	//指向下个键值对
} dictEntry;
```

- **key** 表示的键值对的键；

- **v** 表示键值对的值，**v** 是一个共同体，表示这里的值类型可以是指针、uint64_t、int64_t 和 double 其中之一，用共同体可以节约内存；

- **dictEntrynext** 指向下一组键值对，这里是链表，当需要存储的键值对最后计算得到的存储的位置索引出现重复的时候，就使用链表，将多个键值对存在一个数组元素中，而且，Redis 中，新数据会存储在链表的最前面

## 2.2、dictht

**dictht** 即为 Redis 操作时的值结构，用于保存多组的键值对，声明如下：

```c
typedef struct dictht {
    dictEntry **table;		//键值对数组，数组的元素是个链表
    unsigned long size;		//键值对数组的大小
    unsigned long sizemask;	//掩码，用于计算索引
    unsigned long used;		//键值对数量
} dictht;
```

- **table** 是一个 **dictEntry** 类型的数组指针，它的每个元素都是指针，都指向一个 **dictEntry** 类型；
- **size** 表示键值对数组的大小；
- **sizemask** 为掩码，用于计算键值对插入时的数组索引，它总是 size - 1，后面会再次说到；
- **used** 表示当前哈希表存储的键值对的数量；

下图是一个 dictht 的存储结构，k0和k1的键值计算的索引相同，所以放在一个数组元素中：

![image-20201107211840121](E:\Note\源码阅读笔记\Redis源码\image-20201107211840121.png)

## 2.3、dict

**dict** 是最终的字典的数据结构，声明如下：

```c
typedef struct dictType {
    unsigned int (*hashFunction)(const void *key);
    void *(*keyDup)(void *privdata, const void *key);
    void *(*valDup)(void *privdata, const void *obj);
    int (*keyCompare)(void *privdata, const void *key1, const void *key2);
    void (*keyDestructor)(void *privdata, void *key);
    void (*valDestructor)(void *privdata, void *obj);
} dictType;

typedef struct dict {
    dictType *type;
    void *privdata;
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    int iterators; /* number of iterators currently running */
} dict;
```

- **dictType** 是一组操作函数指针，用于操作特定类型的键值对，Redis 会为不同类型的键值对设置不同类型的函数；

- **privdata** 表示需要传递给操作函数的特定私有数据；

- **ht** 是一个 **dictht** 类型的数组，有两个元素，之所以两个，是因为需要rehash，后面会再次说到；

- **rehashidx** 表示 rehash 进度，-1表示当前并没有进行 rehash；

- **iterators** 表示当前运行的迭代器数量，本次不做特别说明；

下图表示一个没有在rehash的字典

![image-20201107212436924](E:\Note\源码阅读笔记\Redis源码\image-20201107212436924.png)

# 3、字典的存储方法

Redis 中的哈希操作，顾名思义，存储方法肯定和哈希算法有关，这里先简单介绍一下哈希算法。

## 3.1、哈希算法

哈希算法又称之为散列函数，它把任意长度的输入，通过一系列的算法， 变换成固定长度的输出。

Redis 底层使用的哈希算法是 MurmurHash 算法，最初由 Austin Appleby 在2008 年发明，其优势在于，无论输入是否有规律，输出都是随机分布的，而且速度很快。

其实了解哈希的同学都能够明白，用有限的hash值来表示无限的数据，肯定会出现不同的数据得出重复的哈希值的问题，当然，专业的说法不叫重复，叫 碰撞。

## 3.2、存储过程和键冲突

回到正题，现在当一个键值对添加到字典中，会先计算出当前键值对需要存储在 **table** 中的 **index**，计算分两步，先根据键计算hash值，再根据hash值和掩码计算index

```c
hash = dict->type->hashFunction(key);	//根据键计算其hash值
//根据hash值和掩码计算索引，ht[x]可能会是h[0]，也可能是h[1]，根据rehash来定
index = hash & dict->ht[x].sizemask;
```

首先，对于不同的key，这里计算的hash值可能相同，其次，不同的hash值经过和掩码取&，会出现相同的index，这也是使用链表的原因，在 Redis 中称之为键发生了冲突（collision）

我们前面说到，sizemask 总是 size - 1，即数组的最大索引，hash & sizemask 就保证得出的 index一定是一个小于等于 sizemask 的值，即一定在数组内

计算错 index 之后，就把该键值对保存到 table 对应的位置，table 的元素都是链表，新插入的键值对，会保存在链表的最前端，这样效率最高，时间复杂度为 O(1)，如果保存在最后端，那么时间复杂度为O(N)。反正放在最前端和最后端都一样，就取一个插入最快的吧。

这样存储完成之后，我们取数据也就是要在数组和链表中取，时间复杂度也就是 O(1) + O(N)，这里的N越小越好，即链表越小越好，最好没有键冲突，那么时间复杂度就是O(1)

## 3.3、rehash

**rehash** 即对hash表进行扩展和收缩。

这个操作是非常又必要的，可以想象一下，假设一开始创建的 **dictEntry** 数组的大小只有100个，结果随着时间的推移，保存的键值对慢慢变多，变成五六百个，那么，键冲突的概率就会成倍地增加，最后就会导致个别数组内的链表元素有多个，这样就大大地增加了读取的效率，此时就很有必要对原先只有100个元素的数组进行拓展，比如扩展成五六百个，尽量保证，链表节点的数量最小。同理，当保存的键值对删减之后，缩小数组可以节约内存，反正空着也是空着，不如释放了。

提到hash，就不得不提负载因子（load factor）的概念，它是保存的键值对和数组大小的一个比值，使用扩展和收缩的手段，把它控制在一个合理的范围之内，可以避免内存的浪费和读取的低效

负载因子的计算公式如下：

```c
load_factor = ht[0].used / ht[0].size
```

used 是保存的的键值对的数目，size 是数组的大小

可见，如果负载因子太大，表示数组中的元素的链表元素会多，即键冲突的概率会变大；

而如果负载因子太小，表示数组中可能有些元素没有使用，即有些内存浪费了；

**哈希表的收缩和扩展：**

Redis 中，当满足下面两个条件之一时，会自动进行扩展操作：

1. 当服务器没有执行 **BGSAVE** 和 **BGREWRITEAOF** 命令，并且负载因子大于等于1的时候；
2. 当服务器正在执行 **BGSAVE** 和 **BGREWRITEAOF** 命令，并且负载因子大于等于5的时候；

这是因为这两个命令在执行过程中，Redis 需要创建子进程，而大多数的操作系统都采用写时复制的技术来优化子进程的使用效率，所以在子进程存在期间，服务器会提高触发扩展所需的负载因子，尽可能避免在子进程存在期间进行扩展操作，最大限度地节约内存。

当 Redis 进行 rehash 这种操作的时候，客户端还在使用，要兼顾 rehash 和 客户端，就要保证原数据和新数据同时存储和查询。这就是 **dict** 结构中 **ht** 大小为2的原因。

在没有进行 rehash 的时候，只使用 ht[0]，rehash 会将新旧数据都重新散列，存入 ht[1] 中。rehash 完成之后，将 ht[1] 变成 ht[0]，原来的 ht[0] 释放掉，再新建一个 ht[1] 

## 3.4、渐进式rehash

当数据量很大的时候，rehash 操作如果一次性将h[0]数据转到ht[1]，会导致服务宕机，这是不能接受的。因此，Redis 的 rehash 操作并不是一次性、集中式地完成，而是分多次、渐进式地完成的。

大致步骤如下：

1. 为 ht[1] 分配空间，，让字典同时持有 ht[0] 和 ht[1] 两个哈希表；
2. 将 rehashindex 值设置为0，表示开始 rehash；
3. 在 rehash 期间，每次对字典执行增删查改的操作时，程序还会顺带将 ht[0] 中哈希表在 rehashindex 上的所有键值对 rehash 到 ht[1] 上，完成只有，rehashindex 递增；
4. 随着字典的操作的不断执行，最终在某个时间点上，ht[0] 的所有键值对都会被rehash至 ht[1]，这时候，rehash 全部完成，将 rehashindex 设置为-1；

渐进式 rehash 的好处在于，其采用分治的方式，将 rehash 键值对的工作量均摊到每次对字典的增删查改上，避免了集中式 rehash 带来的庞大的计算量

**渐进式 rehash 执行期间的哈希表操作：**

rehash 的时候，ht[0] 和 ht[1] 同时使用，字典的增删查改等操作会先后在 ht[0] 和 ht[1] 上执行，比如查找时，先在 ht[0] 上查找，如果没有找到，则在 ht[1] 上查找。

rehash 的时候，ht[0] 只删改查，不会进行添加操作，添加会直接添加到 ht[1] 中，保证了 ht[0] 中的键值对数量只减不增，直到 rehash 完成之后，ht[0] 变成空表。

# 4、总结

1. 字典广泛用于实现 Redis 的各种功能，包括数据库和哈希键；
2. 字典底层使用哈希表实现，每个字典带有两个哈希表，一个平时使用，一个 rehash 的时候使用；
3. 哈希表使用单项链表来解决键冲突；
4. 哈希表扩展和收缩时，Redis 会将一个哈希表 rehash 到另一个哈希表上，这个操作是渐进式的，不是一次完成的；

















