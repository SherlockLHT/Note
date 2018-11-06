> Shell脚本,就是利用Shell的命令解释的功能，对一个纯文本的文件进行解析，然后执行这些功能，也可以说Shell脚本就是一系列命令的集合。
>  Shell可以直接使用在win/Unix/Linux上面，并且可以调用大量系统内部的功能来解释执行程序，如果熟练掌握Shell脚本，可以让我们操作计算机变得更加轻松，也会节省很多时间。

# Shell应用场景

### 适合的应用场景

- 复杂命令简单化；
- 自动化脚本；
- 替代重复的工作等；

### 不适合的应用场景

- 精密的运算；
- 高效的处理；
- 网络操作等；

## 基本语法

### (1) 解析器

Linux里面不仅仅只有bash一个解析器，其语法会有差异，这句话指定解析器

```shell
#!/bin/bash
```

### （2）运行脚本

```shell
chmod +x ./test.sh	#给脚本权限
./test.sh			#执行脚本
```

### （3）变量

- 等号（=）前后不能有空格；
- 变量前面用$，否则就是纯文本；

```shell
text="Hello World"
num=100
echo $text	#Hello World
echo num	#num
echo $num	#100
```

### （4）四则运算

- 运算符前后必须有空格；
- 乘号（*）需要转义（\\\*）；

```shell
a=10
b=20
c=`expr $a + $b`
d=`expr $a - $b`
e=`expr $a \* $b`
f=`expr $a / $b`
echo $c	#30
echo $d #-10
echo $e	#200
echo $f	#0
```

### （5）其他运算符

| 运算符 | Description |
| ------ | ----------- |
| -o     | 或 |
| -a | 与 |

```shell
a=10
b=20
c=`expr $a % $b`
echo $c

if [ $a == $b ]
then
	echo "a  == b"
else
	echo "a != b"
fi

if [ -o $a ]
then
	echo "yes"
else
	echo "no"
fi

if [ $a -a $b ]
then
	echo "yes"
else
	echo "no"
fi
```

### （6）关系运算符

| 运算符 | Description |
| ------ | ----------- |
| -eq    | 相同返回true |
| -ne | 不同返回true |
| -gt | 大于 |
| -lt | 小于 |
| -ge| 大于等于 |
| -le | 小于等于 |

