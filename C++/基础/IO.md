# 一、文本I/O

## （1）cin和cout

- cin的作用是，负责把字符串转换成其他类型；
- 不管cin需要转换的目标类型是什么，输入的一定是一个字符串；

示例：

假设输入 38.5 19.2

```c++
char ch;
cin >> ch;//将输入的第一个字符3复制给ch

int n;
cin >> n;//不断读取输入，直到遇到不属于int型的字符，转换成int型，复制给n，38
    
double d;
cin >> d;//不断读取，直到不属于double型的字符，转换成double型，复制到d中，38.5

char word[50];
cin >> word;//不断读取，知道遇到空白字符，将字符编码保存到word中，末尾加上以恶搞空字符，38.5
cin.getline(word, 50);//不断读取，直到遇到换行符，末尾加上一个空字符，保存到word中
```

## （2）写文件

写入到文本文件的方法和 cout 相似

头文件 fstream 中定义了一个用于处理输出的 ofstream 类，使用时，用 open() 方法将对象和文件关联起来。而关联起来后，cout 的所有方法都可以用于 ofstream 对象

```c++
ofstream stream;
stream.open("test.txt");
stream << "12345"<<endl;
stream.close();
```

## （3）读文件

读文件和 **cin** 相似

- 头文件 **fstream** 中定义了 **ifstream** 类，用于从文件输入；
- 使用 **open()** 方法将 **ifstream** 对象和文件关联起来；
- 可以使用 **>>** 读取各种类型的数据；
- 可以使用 **get()** 方法读取一个字符，使用 **getline()** 方法读取一行字符；
- 可以使用 **eof()**、**fail()** 方法判断输入是否成功；

## （4）文件的打开方式

使用文件的输入/输入流对文件进行操作的时候，需要设置mode，一般在构造函数和 open() 方法中用到

mode有：

| open mode | 代表意义 | 描述 |
| --------- | ---- | ---- |
| in        | input | 打开文件供读取 |
| out | output | 打开文件供写入 |
| binary | binary | 以二进制的模式操作文件，而不是文本 |
| ate | at end | 输出位置从文件末尾开始 |
| app | append | 追加的模式输出文件 |
| trunc | truncate | 写入的时候覆盖原先内容 |

mode在使用时需要使用命名空间，多个 mode 之间用 **|** 间隔

示例：

```c++
//读文件
std::ifstream ifs;
ifs.open ("test.txt", std::ifstream::in);
//写文件
std::ofstream ofs;
ofs.open ("test.txt", std::ofstream::out | std::ofstream::app);
```

# 二、读写文件常用方法

下面只列出一些常用的文件流方法

## 1、通用方法

| 方法 | 说明 | 参数 | 返回值 |
| ---- | ---- | ---- | ---- |
| open | 打开文件流 | 文件名、open mode | none |
| is_open | 检查文件是否打开 | 无 |true/false|
| close | 关闭文件流 | 无 |none|
| rdbuf | 获取流缓冲区 | 无 |filebuf指针|
| operator= | 流对象赋值 | 无 |none|
| swap | 文件流交换 | ifstream对象 |none|

## 2、ofstream - 文件输出流

继承自 **ostream**

| 方法       | 说明 | 参数 | 返回值 |
| ---------- | ---- | ---- | ------ |
| operator<< | 插入格式化输出 | 字符串 | none |
| put | 插入字符 | char |*this|
| write | 插入字符串 | 字符串，size |*this|
| tellp | 获取输出序列的位置 | 无 |返回stream中的为位置|
| seekp | 设置输出序列的位置 | 无 |*this|
| flush | 刷新输出流缓冲区 | 无 |*this|

## 3、ifstream - 文件输入流

继承自 **istream**

| 方法       | 说明 | 参数 | 返回值 |
| ---------- | ---- | ---- | ------ |
| operator>> | 读取格式化输入 |      |        |
| gcount | 获取字符数量 | | streamsize类型 |
| get  | 读取一个字符，使用方法有两种 | char<br>无 | *this<br>int |
| getline  | 读取一行字符串 | char*、字符数 | *this |
| ignore  | 读取和删除字符<br>提取并删除n个字符，或者直到delim | 字符数、delim | |
| peek  | 返回序列中的下一个字符，但是不提取之<br>即文件位置还在原处 | | int |
| read  | 读取特定数量的字符串 | char*、字符数 | *this |
| readsome  | 从流中提取字符串 | char*、字符数 | *this |
| putback  | 在流中的当前位置放入一个字符 | char | *this |
| unget  | | | *this |
| tellg  | | | |
| seekg  | | | |
| sync  | | | |

**示例：**

```c++
std::cin.ignore(256, ' ');	//提取并删除256个字符，或者直到空格
```
一次读取全部文件：
```c++
std::ifstream stream;
stream.open("test.json");
stringstream buffer;
buffer<<stream.rdbuf();
stream.close();

string jsonStr = buffer.str();
```

