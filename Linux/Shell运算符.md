### （1）四则运算

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

### （2）布尔运算符

| 运算符 | Description |
| :----: | :---------: |
|   -o   |     或      |
|   -a   |     与      |
|   !    |   非运算    |

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

### （3）逻辑运算符

| 运算符 | Description |
| :----: | :---------: |
|   &&   |     AND     |
|  \|\|  |     OR      |

### （4）关系运算符

| 运算符 | Description  |
| :----: | :----------: |
|  -eq   | 相同返回true |
|  -ne   | 不同返回true |
|  -gt   |     大于     |
|  -lt   |     小于     |
|  -ge   |   大于等于   |
|  -le   |   小于等于   |

### （5）字符串运算符

|  运算符   |    Description    |
| :-------: | :---------------: |
|     =     |     判断相等      |
|    !=     |    判断不相等     |
| -z string |  判断长度是否为0  |
| -n string | 判断长度是否不为0 |

### （6）文件运算符

| 运算符  |    Description    |
| :-----: | :---------------: |
| -d file |    是否是目录     |
| -r file |     是否可读      |
| -w file |     是否可写      |
| -x file |    是否可执行     |
| -s file |     是否为空      |
| -e file | 文件/目录是否存在 |

