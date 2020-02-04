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
| forward_list| 前向链表 |
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
- **数组**，可以快速地根据下标获取元素；
- 固定大小的数组对象，用于取代使用C语言风格的数组；
- 构造的时候需要指定其可存储的元素size；
- 数据存储在连续的内存中；
- 支持迭代器访问；

## 2、vector

- **动态数组**，可以快速地根据下标获取元素；
- 和array的区别在于，vector可以动态改变size；
- 使用时，非必需指定size，可以根据添加元素的数量动态调整size；
- 数据存储在连续的内存中；
- 支持迭代器；

## 3、deque

- **双端队列**，是一种线性表，类似vector和list的结合体，算是两者优势的一个折中；
- 支持快速随机访问；
- 可以在两端扩展和收缩；
- 数据存储在非连续的内存中；
- 支持迭代器；

优点：

- 使用类似 **动态数组** 的实现，具有数组的快速访问元素的优势；
- 因为是双端队列，头尾操作效率高；

缺点：

- 因为中间插入元素效率低；

## 4、forward_list

- **前向链表**，单链表，只能从头开始向前迭代；

优点：

- 插入、删除和移动内部元素效率比数组高；

缺点：

- 访问内部元素效率较数组低；

## 5、list

- **双向链表**，和 **forward_list** 的区别在于双向；

## 6、stack

- 堆栈，先入后出，仅能从容器的一端插入和提取；

## 7、queue

- 单端队列，先入先出；

## 8、priority_queue

- 优先队列，和queue的区别是，新增了一个排序方法，可以自定义优先级；

## 9、set

- **集合**，按照特定顺序存储元素，且元素唯一；
- 和 **map** 的区别在于没有value，只有key；
- 使用红黑树实现，元素按照一定的顺序排序，也可以自定义排序方法；

## 10、multiset

- 和 **set** 的区别在于，元素不唯一；

## 11、map

- **映射**，比 **set** 多一个映射关系；

```c++
map<int, string> temp;
temp.insert(make_pair(1, "a"));
temp.insert(make_pair(2, "b"));
temp.insert(make_pair(1, "c"));//因为key为已经存在，则不插入，返回key为1的迭代器
//迭代器的使用
map<int, string>::iterator iter = temp.begin();
for(; iter != temp.end(); ++iter)
{
	cout<<iter->first<<":"<<iter->second<<endl;
}
```

## 12、multimap

- 和map的区别在于key可以重复；

multimap迭代器的使用和map有所不同：

```c++
multimap<int, string> temp;
temp.insert(pair<int, string>(1, "a"));
temp.insert(pair<int, string>(2, "b"));
temp.insert(pair<int, string>(1, "c"));
multimap<int, string>::iterator it = temp.begin();
for(; it != temp.end(); ++it)
{
	cout<<(*it).first<<":"<<(*it).second<<endl;
}
```

## 13、unordered_xxx

unordered__xxx包括无序的关联容器，以set举例，**unordered_set** 和 **set** 比较：

**优点：**

- 基于hash表实现，插入和查找的效率比set高；

**缺点：**

- 优点的代价是占用内存大，无法自动排序，使用一个下标比较大的数组来存储元素，形成很多桶；

**何时使用使用set：**

1. 需要有序数据，且去重；
2. 需要访问元素；
3. 需要访问指定元素的前后元素；

**何时使用使用unordered_set：**

1. 需要去重，但不需要排序；
2. 需要单个访问元素，且不需要遍历；