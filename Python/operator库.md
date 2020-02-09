# 说明

python 的 operator 模块提供了一套方法，效果和python的内置运算符相同，运行效率高，作为标准运算符的替代函数

例如，```operator.add(a, b)``` 和 ```a + b``` 相同，

# 常用方法

| 方法 | 操作符 | 操作说明 |
| :------: | :----: | :--: |
| add(1, b) | a + b | 加法 |
| concat(a, b) | a + b | 字符串拼接 |
| contains(a, b) | a in b | b包含a |
| truediv(a, b) | a / b | 除法 |
| floordiv(a, b) | a // b | 除法，取整 |
| and\_(a, b) | a & b | 按位与 |
| xor(a, b) | a ^ b | 按位异或 |
| invert(a) | ~ a | 按位取反 |
| or\_(a, b) | a \| b | 按位或 |
| pow(a, b) | a ** b | 取幂 |
| is\_(a, b) | a is b | 是否相同 |
| is\_not(a, b) | a is not b | 是否不相同 |
| setitem(obj, k, v) | obj[k] = v | 按下标赋值 |
| delitem(obj， k)| del obj[k] | 按下标删除 |
| getitem(obj， k)| obj[k] | 按下标取值 |
| lshift(a， b)| a << b | 左移 |
| mod(a， b) | a % b | 取模 |
| mul(a， b) | a * b | 乘法 |
| matmul(a， b) | a @ b | 矩阵乘法 |
| neg(a) | - a | 取负 |
| not\_(a) | not a | 逻辑判断 |
| pos(a) | + a | 取正 |
| rshift(a, b) | a >> b | 右移 |
| setitem(seq， slice(i， j)， values) | seq[i: j] = values | 切片赋值 |
| delitem(seq， slice(i， j)) | del seq[i: j] | 切片删除 |
| getitem(seq， slice(i， j)) | seq[i: j] | 切片取值 |
| mod(s， obj) | s % obj | 字符串格式化 |
| sub(a， b) | a - b | 减法 |
| truth(obj) | obj | 判断真 |
| lt(a， b) | a < b | 小于比较 |
| le(a， b) | a <= b | 小于等于比较 |
| eq(a， b) | a == b | 等于比较 |
| ne(a， b) | a !=b | 不等于比较 |
| ge(a， b) | a >= b | 大于等于比较 |
| gt(a， b) | a > b | 大于比较 |

## 3.4新增方法

operator 模块还新增了一些用于常规属性和条目查找的工具

***operator.attrgetter(attr)***

返回从其操作数获取attr的可调用对象。如果请求多个属性，则返回属性元组

**源码：**

```python
def attrgetter(*items):
    if any(not isinstance(item, str) for item in items):
        raise TypeError('attribute name must be a string')
    if len(items) == 1:
        attr = items[0]
        def g(obj):
            return resolve_attr(obj, attr)
    else:
        def g(obj):
            return tuple(resolve_attr(obj, attr) for attr in items)
    return g
 
def resolve_attr(obj, attr):
    for name in attr.split("."):
        obj = getattr(obj, name)
    return obj
```

**示例：**

```python
class Student:
    def __init__(self, name, grade, age):
        self.name = name
        self.grade = grade
        self.age = age
    def __repr__(self):
        return repr((self.name, self.grade, self.age))

# 建立一组Student对象
students = [
    Student('jane', 'B', 12),
    Student('john', 'A', 12),
    Student('dave', 'B', 10),
]

print(sorted(students, key=operator.attrgetter('age')))	#根据age属性值进行排序
```

**operator.itemgetter(item)**

返回一个可调用对象，该对象使用操作数的 **\_\_getitem\_\_()** 方法从其操作数中获取项。如果指定了多个项，则返回一个查找值元组

**源码：**

```python
def itemgetter(*items):
    if len(items) == 1:
        item = items[0]
        def g(obj):
            return obj[item]
    else:
        def g(obj):
            return tuple(obj[item] for item in items)
    return g
```

**示例：**

```python
operator.itemgetter(1)('ABCDEFG')	# B
operator.itemgetter(1, 3, 5)('ABCDEFG')	# ('B', 'D', 'F')
operator.itemgetter(slice(2, None))('ABCDEFG')	# CDEFG
```

**operator.methodcaller(name, args...)**

获取对象的方法

**示例：**

```python
class Student(object):
    def __init__(self, name):
        self.name = name

    def getName(self, str):
        return (self.name + str)

stu = Student("sherlock")
func = methodcaller('getName', 'aaa') # 得到getName方法，传入参数
print(func(stu))	# sherlockaaa
```




