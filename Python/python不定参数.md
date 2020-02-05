<font size = 3>

# 普通参数

python可以按照一般传参的方式调用函数

```python
def Function(name):
    print name
```

# 不定参数

在普通参数的数量或者类型不确定的时候，可以使用不定参数，不定参数可以分为两种：参数数量不定和参数类型不定

## 可变参数

即是参数的数量不确定，使用 **\*** 即可传递可变参数

python中其实是在调用时将参数组成一个tuple 传入，以达到可变参数的目的

示例：

```python
def Function(*kw):
    print kw
Function(1, 2, 3, 4)	#(1, 2, 3, 4)
```

## 关键字参数

关键字参数可以认为调用时，指定某个参数是某个值，即传入包含参数名的参数，使用**\*\***

可变参数是拼接成tuple，而关键字参数则是拼接成dict

示例：

```python
def Fun(**kwargs):
    print kwargs
Fun(a=11, b=22, c=33)	#{'a': 11, 'c': 33, 'b': 22}
```

## 参数的传递顺序

需要注意的是，不定参数的定义和传递需要按照一定的顺序，**普通参数、可变参数、关键字参数**

```python
# -*- encoding: utf-8 -*-

def Fun(name, *kw, **kwargs):
    print name
    print kw
    print kwargs

Fun(1, 2, 3, a=11, b=22, c=33)
```

如上，输入：

```cmd
1
(2, 3)
{'a': 11, 'c': 33, 'b': 22}
```

如果函数有不定参数的时候，需要把普通参数写在最前面

