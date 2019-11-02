# 一、string和basic_string

**basic_string** 是一个模板类，**string** 是模版形参为 **char** 的 **base_string** 的类型定义

源码如下：

```c++
template<typename _CharT, typename _Traits, typename _Alloc>
    class basic_string
    {
        ...
    }

typedef basic_string<char>    string;  
```

使用 **base_string** 为模版的字符串类有四种：**string**、**u16string**、**u32string** 和 **wstring**

**string::npos** 的初始化为-1，一般用来表示没有找到，所以，string的find方法没没有找到可以这样写：

```c++
if(str.find("aaa") != string::npos)
{...}
```

# 二、常用方法

参见手册：http://www.cplusplus.com/reference/string/string/

## 1、basic_string方法

### （1）string转换成数字

**stoi**：string to int

**stol**：string to long int

**stoul**：string to unsigned int

**stoll**：string to long long

**stof**：string to float

**stod**：string to double

**stold**：string to long double

### （2）数字转换成string

**to_string**：数字 to string

**to_wstring**：数字 to wide string

### （3）迭代器

**begin**：迭代器，指向开头

**end**：迭代器，指向结尾

## 2、string类

### （1）迭代器

**rbegin/rend**：反向的迭代器

**cbegin/cend**：const迭代器

**crbegin/crend**：const的反向迭代器

### （2）容量

需要注意的事，string类所占的内存空间并不全是存字符串的

**size/length**：但会字符串的长度

**max_size**：返回最大可以存储的字符数量

**resize**：重新定义字符串长度，多余部分舍弃，注意这里是字符串长度，和 **size/length** 有关

**capacity**：返回为当前string对象分配的存储空间大小，以字节表示

**reserve**：重新分配string对象的内存大小，和 **capacity** 有关，主要是为了预留空间，避免重复分配内存

**clear**：清除字符串，变成空字符串，并不会影响所占内存空间

**empty**：检查是否为空字符串

**shrink_to_fit**：将string对象的内存缩小至最合适的大小，根据字符串大小，避免多余空间

### （3）读取元素

**[]**：获取元素，同字符数组用法，超出范围返回空字符

**at**：获取元素，超出范围，抛出 **out_of_range** 异常

**back**：最后一个字符

**fornt**：第一个字符

### （4）修改内容

**+**：重载的+，追加字符串

**append**：追加字符串/字符

**push_back**：只能追加字符

**assign**：重新赋值字符串，替换当前字符串

**insert**：在指定位置插入字符或者字符串

**earse**：删除指定位置的字符串

**replace**：替换指定位置的字符串

**swap**：交换字符串

**pop_back**：删除最后一个字符