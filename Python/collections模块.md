# 一、模块简介

collections模块包含了一些容器类，作为python的标准内建容器的功能补充

本文以python3.6的版本简要说明，因为各个版本的方法可能有所不能，这里只列出一些常用的功能，详细变更可参考官方文档：https://docs.python.org/zh-cn/3/library/collections.html

python的常用的标准内建容器有：dict、list、set 和 tuple

collections 模块包含的容器有：

| 容器    | 说明 |
| ------- | ---- |
| Counter | dict子类，用于集合计数 |
| defaultdict | dict子类，带有默认值的dict |
| OrderedDict | dict子类，新增了排序的功能 |
| namedtuple | 创建一个带有名称的tuple |
| deque | 双向列表 |
| ChainMap | dict 列表，将多个dict链接起来 |
| UserDict | dict的封装 |
| UserList | list的封装 |
| UserString | string的封装 |

# 二、容器类

## 1、**ChainMap**

当需要将多个dict合并成一个单元的时候，我们普通的方法是新建一个数据类型，再将dict的数据合并进去，但是这样有个问题就是，如果原始的dict改变了，新创建的数据类型不会发生变化，需要再次手动update

**ChainMap**故名思议，就是将dict链接起来，其作用就是将多个dict合并成一个的同时，还能自动同步原始的数据结构

```python
dict1 = {'a': 1, 'b': 2}
dict2 = {'a': 99, 'c': 3, 'd': 4}
chain_map = collections.ChainMap(dict1, dict2)
print(chain_map)
dict1['f'] = 5
print(chain_map)
```

输出：

```python
ChainMap({'a': 1, 'b': 2}, {'a': 99, 'c': 3, 'd': 4})
ChainMap({'a': 1, 'b': 2, 'f': 5}, {'a': 99, 'c': 3, 'd': 4})
```

可见，**ChainMap** 类型虽然合并了dict，但是用的还是原来的数据

**ChainMap** 在操作的时候，只会操作第一个dict

```python
chain_map['a'] = 2
print(chain_map)
print(chain_map['a'])	# 只打印了第一个dict
print(dict1['a'])	# 2，被修改
print(dict2['a'])	# 99，未被修改
```

输出：

```python
ChainMap({'a': 2, 'b': 2, 'f': 5}, {'a': 99, 'c': 3, 'd': 4})
2
2
99
```

**总结：**

**ChainMap** 原理上是开辟了一个队列，用于保存原始的dict的引用

```python
chain_map = collections.ChainMap({'a': 1})	# ChainMap({'a': 1})
dict1 = chain_map.new_child()		# ChainMap({}, {'a': 1})
dict1['a'] = 2			# ChainMap({'a': 2}, {'a': 1})
dict1.parents			# ChainMap({'a': 1})
dict1.parents['a'] = 99	# 就是chain_map，修改会直接改变值
print(chain_map)		# ChainMap({'a': 99})
```


**new_child()** 方法就是在原来的dict链表的最前放插入一个新的空dict

**parents** 就是调用前的 **ChainMap** 对象

**maps** 是 **ChainMap** 的一个列表，包含内部的所有dict
```python
chain_map = collections.ChainMap({'a': 1, 'b': 2})
dict1 = chain_map.new_child()
print(dict1)
print(dict1.maps)	# [{}, {'a': 1, 'b': 2}]
```

## 2、Counter

**Counter** 一般用于对集合（list、set、tuple、dict）进行元素的计数

```python
list = ['a', 'b', 'c', 'c', 'c']
cnt = collections.Counter(list)	# Counter({'c': 3, 'a': 1, 'b': 1})
cnt.elements()
cnt[3]	# 元素c一定出现3次
cnt.most_common(2)	# list类型，出现次数最多的2个元素
del cnt['c']	# 删除元素c的次数值，Counter({'a': 1, 'b': 1})
```

**Counter** 还支持一些操作符

```python
c = Counter(a=3, b=1)
d = Counter(a=1, b=2)
e = c + d    # 相加， Counter({'a': 4, 'b': 3})
e = c - d    # 相减，如果小于等于0，删去， Counter({'a': 2})
e = c & d    # 求最小，Counter({'a': 1, 'b': 1})
e = c | d    # 求最大，Counter({'a': 3, 'b': 2})
```

## 3、deque

双向队列
**常用方法：**

| 方法     | 说明 |
| -------- | ---- |
| append() | 右侧添加元素 |
| appendleft() | 左侧添加元素 |
| clear() | 清空所有元素 |
| copy() | 浅拷贝 |
| count(x) | 计算x元素的数量 |
| extend(iterator) | 使用iterator元素在右侧拓展 |
| extendleft(iterator) | 使用iterator元素在左侧拓展 |
| index(x, start, stop) | 返回从start和stop之间的第一个值为x个元素的下标<br>没找到则抛出ValueError异常 |
| insert(index, x) | 在index未知插入元素x |
| pop() | 删除最右侧元素，并返回该元素 |
| popleft() | 删除最左侧元素，并返回该元素 |
| remove(x) | 删除第一个值为x的元素，返回None<br>没找到，则抛出ValueError异常 |
| reverse() | 将deque逆序，返回None |
| rotate(n = 1) | deque个元素向右循环n个位置，n为负值，则向左循环 |
**maxlen** 表示 **deque** 的最大size，没有限定，则为None

## 4、defaultdict

在使用dict的时候，如果key不存在，则会抛出 KeyError 异常，为了避免这种情况的，可以使用 **defaultdict** 来位dict提供默认值

**defaultdict** 和 **dict** 的区别在于，**defaultdict** 会对不存在的key返回一个默认值，该值根据 **default_factory** 不同而改变

**示例：**

将 list() 方法作为 default_factory，则访问没有定义的key时，产生一个空的list作为默认值

```python
d = collections.defaultdict(list)
print(d['a'])	#[]
```
将 int() 方法作为 default_factory，则访问没有定义的key时，产生一个整形数字0作为默认值
```python
d = collections.defaultdict(int)
print(d['a'])	#0
```
自定义方法作为 default_factory，则访问没有定义的key时，使用方法的返回值作为默认值
```python
def fun():
    return 123

d = collections.defaultdict(fun)
print(d['b'])	#123
```

## 5、namedtuple

namedtuple 和 tuple 之间的区别在于，namedtuple 位tuple添加了一个名称

声明如下：

```python
collections.namedtuple(
    typename, 		# 名称
    field_names, 	# 字段
    *,				# *表示任何python标识符都可以作为字段名
    rename=False, 	# 若为True，则无效字段会自动转换成未知
    defaults=None, 	# 默认值，字段从右向左设定，3.7新增
    module=None		# 
)
```

**优点：**

命名元组实例没有字典，所以它们要更轻量，并且占用更小内存

**示例：**

```python
nameTuple = collections.namedtuple('Test', 'name, age, id')
tuple = nameTuple('Tom', 28, 464643123)

print(tuple)		# Test(name='Tom', age=28, id=464643123)
print(tuple.age)	# 28，访问字段
```

字段的定义可以是list，也可以是字符串，用空格或者逗号隔开

下面的两种写法和上例等效

```python
nameTuple = collections.namedtuple('Test', 'name age id')
```

```pthon
nameTuple = collections.namedtuple('Test', 'name, age, id')
```

**常用方法：**

namedtuple 继承了tuple 的方法外，还新增了一些方法：

**tuple._make(iterator)**

根据传入的参数创建一个新的namedtuple

```python
nameTuple = collections.namedtuple('Test', 'name, age, id')
tuple = nameTuple('Tom', 28, 464643123)
print(tuple)	# Test(name='Tom', age=28, id=464643123)
t = ['sherlock', 99, 11111]
tuple = tuple._make(t)	# 这里传入的是字段参数，要和构建字段的方法相同
print(tuple)	# Test(name='sherlock', age=99, id=11111)
```

**tuple._fileds**

返回一个tuple，元素是所有字段

```python
nameTuple = collections.namedtuple('Test', 'name, age, id')
tuple = nameTuple('Tom', 28, 464643123)
print(tuple._fields)	# ('name', 'age', 'id')
```

**tuple._field_defaults**

返回dict，包含各字段的默认值

## 6、OrderedDict

内建的 dict 会在保存元素的时候保留插入的顺序，OrderedDict 除了继承了dict的一些特性外，好新增了排序的功能

OrderedDict 则 和 dict 的区别在于：

1. 新增重新排序；
2. 重新排序效率高；
3. 相等操作会匹配顺序；
4. 个别方法不同；
5. 删除并重新插入元素后，该元素移动到最后；

**常用方法：**

**OrderedDict.popitem(last=True)**

和 dict 相比有一些修改，dict 的 popitem 方法只能将最后一个元素删除，而OrderedDict 的 popitem 方法接受一个参数last，用于控制最后一个元素的定义

当 last 为 True 时，符合 LIFO 顺序，则 last 表示最后一个元素；

当 last 为 False 时，按照 FIFO 顺序，则 last 表示第一个元素；

**示例：**

```python
od = OrderedDict({'banana': 3, 'apple': 4})
od.popitem(False)
print(od)	# OrderedDict([('apple', 4)])
```

**OrderedDict.move_to_end(key, last=True)**

将元素 key 移动到最后，last 用于控制最后一个元素的定义

## 7、UserDict、UserList、UserString

对 dict/list/string 再次进行一层封装，方便用户自定义数据类型

原本位于 UserDict、UserList、UserString 模块中，后移到 collections 模块内，

