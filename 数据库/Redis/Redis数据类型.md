[TOC]

# 数据类型

支持五种数据类型：string、hash、list、set（集合）和 zset（sorted set，有序集合）；

| 类型 | 简介 | 特性 | 使用场景 |
| ---- | ---- | ---- | ---- |
| string | 字符串 | 二进制安全，可以包含任意数据 |  |
| hash | key-value | 适合存储对象 | 存储/读取/修改用户属性 |
| list | 链表 | 增删快，提供操作一段元素的API | 消息队列等 |
| set | hash表实现 | 增删查复杂度O(1) | 利用唯一性，可以统计一些数据 |
| zset | set类型增加一个score | 数据插入时，已经排序 | 排行榜、消息队列等 |

##  1、string

- 最基本类型；
- 最大能存储512M；
- 二进制安全，即可以包含任何数据，包括图片数据或者序列化对象；

**基本指令：SET 和 GET**

```cmake
SET str "字符串"
OK

GET str
字符串
```

## 2、hash

- 最多可以保存2^32 - 1组key-value；

**基本指令：HSET 和 HGET**

```cmake
HSET hash_test key1 "测试"
OK

HGET hash_test key1
测试
```
## 3、list

- 字符串链表；
- 按照插入顺序排序；

**基本指令：LPUSH 和 LRANGE**

```cmake
LPUSH list_test redis
1
LPUSH list_test mysql
2
LPUSH list_test postgres
3

LRANGE list_test 0 100
postgres
mysql
redis
```

## 4、set

- 不重复且无序的字符串元素集合；
- 使用hash表实现，添加/删除/查找的时间复杂度都是O(1)；
- 每个set对多可以存 2^32 - 1个成员；

**基本指令： SADD 和 SMEMBERS**

```cmake
SADD set_test abcdefg
1
SADD set_test 测试
1

SMEMBERS set_test
abcdefg
测试
```

## 5、zset（sorted set）

- 不重复但有序的字符串元素集合；
- 每个元素均关联一个double类型的score，redis根据score进行从小到大排序；
- score可以重复，重复的按照插入顺序进行排序；

**基本指令：ZADD 和 ARANGEBYSCORE**

```cmake
ZADD zset_test 0 abc
1
ZADD zset_test 0.12 pp
1
ZADD zset_test 0.11 gg
1
ZADD zset_test 0 bc
1

ZRANGEBYSCORE zset_test 0 1
abc
bc
gg
pp
```

