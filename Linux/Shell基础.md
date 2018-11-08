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

# 基本语法

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
- 使用变量时，前面加$，否则就是纯文本；
- `readonly`定义只读变量，只读变量不能被删除；
- `unset`删除变量，被删除的变量不能再使用；

```shell
text="Hello World"
readonly num=100	#num变量值不能改变
echo $text	#Hello World
echo num	#num
echo $num	#100
```

### （4）字符串

#### 1.引号

- 字符串可用单/双引号，也可以不使用引号；
- 双引号里面可以有转义字符；
- 字符串内可以有变量，但引号内的变量需要用单引号括起，双引号内无需；
- 字符串内的变量可以用{}括起来，也可以不用；

```shell
temp="123"
str='test$temp‘asd’'		#不能有任何转义字符
str2="test$temp 'a' \"a\""	#可以有转义字符
str3='test'$temp''
str4='test'${temp}''	#{}只括变量，不括$
echo $str	#test$tempasd
echo $str2	#test123 'a' "a"
echo $str3	#test123
echo $str4	#test123
```

#### 2.字符串处理

- 利用字符串内可以有变量的特性，组合字符串；

```shell
str1='123'
str2='abc'
str3=''$str1''$str2''
str4="$str1$str2"
echo $str3	#123abc
echo $str4	#123abc
```

```shell
str='abcdefg'
echo ${#str}	#7，获取字符串长度
echo ${str:1:2}	#bc，从index为1出截取2个字符
echo `expr index "$str" ce`	#3，输出c/e的位置，从1开始计算
```

### （5）数组

- 用()包括，空格分开；
- 支持一维数组，不支持多维数组；
- 不限定数组大小；

```shell
array=(1 'a' 2 3)	#定义数组
echo $array			#1,不用下标默认第一个元素
echo ${array[1]}	#a，使用下标访问元素第二个元素
array[1]=123		#改变元素
echo ${array[1]}	#123
echo ${#array}		#1，第一个元素的长度为1
echo ${#arrry[*]}	#4，数组长度
```

### （6）输出

#### 1. echo

- `-e`开启转义；
- `\c`不换行；

```shell
echo -e "hello \nworld"	#两个单词各占一行
echo -e "hello \c"
echo "world"			#和上一行一起输出hello world
echo "hello world">111	#内容定向至文件
echo `date`		#显示执行命令的结果
```

#### 2. printf

- 同C语言；

### (7) test指令

- 用于检查某个条件是否成立；

```shell
num1=100
num2=200
if test $num2 -eq $num2
then
	echo "相等"
else
	echo "不相等"
fi
```

### (8) 逻辑语法

#### if判断

```shell
if condition
then
	do_some_thing
elif
	do_some_thing
else
	do_something
fi
```

```shell
if [ condition ]; then do_some_thing; fi	#写成一行
```
#### for循环
```shell
for var in item1 item2 item3
do
	do_some_thing
done
```

```shell
for var in item1 item2; do so_some_thing; done;
```

```shell
for loop in 1 2 3 4 5
do
	do_some_thing
done
```

```shell
for str in 'abcdefg'
do
	echo $str
done
```

#### while循环

```shell
while condition
do 
	do_some_thing
done
```

#### case

```shell
num=1
case value in
1) 
	doSomeThing
	;;
2)
	doSomeThing
	;;
*)
	doSomeThing
	;;
esac
```

#### 跳出循环

```shell
break
continue
```



# 函数及其参数

### （1）执行时传递参数

```shell
echo "执行的文件名：$0"	#文件名
echo "第1个参数：$1"		 #参数1
echo "第2个参数：$2"
echo "第10个参数： ${10}"	#第10个参数不是$10，而是s{10}
```

| 参数处理 |            Description            |
| :------: | :-------------------------------: |
|    $#    |             参数个数              |
|    $*    |             所有参数              |
|    $@    |              类似$*               |
|    $$    |         脚本运行进程ID号          |
|    $?    | 最后命令的推出状态，0表示没有错误 |

### （2）shell函数

- 函数获取参数的方式和执行时传参相同；

```shell
testFunction(){			#定义函数
	num=`expr $1 + 1`
	num10 = ${10}
	return $num
}
testFunction 123	#调用函数
echo $?		#获取函数返回值
```

# 输入和输出重定向

- 输入和输出默认时终端，重定向即重新定向到别处，比如文件；

|      命令       |                   Description                    |
| :-------------: | :----------------------------------------------: |
| command > file  |              输出重定向到文件，覆盖              |
| command >> file |              输出重定向到文件，追加              |
| command < file  |                 输入重定向到文件                 |
|    n > file     |       将文件描述符为 n 的文件重定向到 file       |
|    n >> file    | 将文件描述符为 n 的文件以追加的方式重定向到 file |
|    n >& file    |              将输出文件 m 和 n 合并              |
|     n <& m      |              将输入文件 m 和 n 合并              |
|     << tag      | 将开始标记 tag 和结束标记 tag 之间的内容作为输入 |

# Shell文件包含

- 包含外部脚本文件；
- 被包含的文件不需要添加可执行权限；

```shell
#temp.sh
str="123 test"
```

```shell
#test.sh
#. ./11.sh		#使用.来包含，.后面有一个空格
source ./11.sh	#使用source包含
echo $str		#123 test
```

