参考：http://www.cplusplus.com/reference/

# 一、STL Containers

STL容器使用类模板实现，是的所支持的元素具有很大的灵活性。

常用的STL容器有：

**（1）顺序容器**

**array** 和 **forward** 为c++11新增

| Containers | Description |
| ---------- | ----------- |
| array | 固定大小的数组 |
| vector     | 可以改变大小的数组 |
| deque | 双端队列 |
| forward_list| 单向链表 |
| list | 双向链表 |
**（2）容器适配器**

| Containers | Description |
| ---------- | ---------- |
| stack | 栈，后进先出 |
| queue | 队列，先进先出 |
| priority_queue | 优先队列，可以自定义元素的out优先级 |
**（3）关联容器**

| Containers | Description |
| ---------- | ---------- |
| set | 集合，只有key没有value，key值唯一，特定顺序 |
| multiset | 集合，只有key没有value，允许key有重复值，特定顺序 |
| map | key-value，key唯一，按照key特定顺序排序 |
| multimap | key-value，允许key重复，按照key特定顺序排序 |
**（4）无序关联容器**

c++11新增

| Containers | Description |
| ---------- | ---------- |
| unordered_set | 和set的区别是没有特定顺序 |
| unordered_multiset | 和multiset的区别是没有特定的顺序 |
| unordered_map | 和map的区别是没有特定顺序 |
| unordered_multimap | 和multimap的区别是没有特定顺序 |

## 迭代器

容器若允许使用迭代器，则包含以下：

| iterators | description |
| --------- | ----------- |
| begin     | 正向迭代器的开头，第一个元素 |
| end | 正向迭代器的结尾，最后一个元素 |
| rbegin | 反向迭代器的开头，最后一个元素 |
| rend | 反向迭代器的结尾，第一个元素 |
| cbegin | 正向const迭代器的开头 |
| cend | 正向const迭代器的结尾 |
| crbegin | 反向cosnt迭代器的开头 |
| crend | 反向const迭代器的结尾 |

- 反向迭代器中的 **r** 表示 **right**，表示从右边/最后开始，++之后迭代器向前迭代；

- 迭代器中的 **c** 表示 **const**，表示这是一个const迭代器；

# 二、顺序容器

## 1、array

- C++11新增；
- 数组，可以根据下标获取元素；
- 固定大小的数组对象，用于取代使用C语言风格的数组；
- 构造的时候需要指定其可存储的元素size；
- 支持C语言风格的元素访问，即[]操作符；
- 支持迭代器访问；

## 2、vector

- 数组，可以根据下标获取元素；
- 可以动态改变size的数组对象；
- 使用时，非必需指定size，可以根据添加元素的数量动态调整size；
- 支持C语言风格的元素访问；
- 支持迭代器；

## 3、deque

- 队列，且为双端队列，是一种线性表；
- 可以在两端扩展和收缩；
- 支持迭代器；

使用类似动态数组的实现，具有快速访问元素的优势，也可以快速在头和尾插入元素







