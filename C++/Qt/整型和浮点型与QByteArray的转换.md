[TOC]

## QByteArray

> The QByteArray class provides an array of bytes.

**Qt** 手册中描述 **QByteArray** 为 **字节数组** ，即是一个数组，里面保存字节。

在 **Qt** 中，QByteArray一般用于数据的传输，因为经常需要将其他类型的数据转换成 **QByteArray**，可以使用它的 **append()** 方法将字节一个一个的插入到数据中。

方法声明如下：

```c++
QByteArray& append(char ch);
```

**append**() 有多个重载，但是只有这个是一个字节一个字节地插入。

## 整型 <==> QByteArray

**以x86平台的int型为例，int占4位，转换后QByteArray大小应该为4，QByteArray大小决定了存储的数据的范围**

遍历 **int型** 数据的四个字节，将每个字节的数据单独提取出来

```c++
static int andArray[4] = {0x000000FF, 0x0000FF00, 0x00FF0000, 0xFF000000};
QByteArray Int2Byte(int int_data, int bits)
{
    QByteArray byte_data;
    bits = (bits > 4 || bits <= 0)? 4: bits;//1/2/3/4

    for(int index = 0; index < bits; index++)
    {
        int offset = index * 8;
        byte_data.append((char) ((andArray[index] & int_data) >> offset));
    }
    return byte_data;
}
int Byte2Int(QByteArray bytes)
{
    int addr = bytes[0] & 0x000000FF;
    addr |= ((bytes[1] << 8) & 0x0000FF00);
    addr |= ((bytes[2] << 16) & 0x00FF0000);
    addr |= ((bytes[3] << 24) & 0xFF000000);
    return addr;
}
```

**说明：**

- **data & 0x0000FF00** 可以保留低二位的数据，清楚其他位数据；
- **data >> 4** 向右偏移 **4** 位，新增位置 **0**，可以将低二位变为低一位；
- **a |= b** 即 **a = a|b**，这里偏移之后，将新位置数据合并起来；
- **byte** 转 **int** 和 **int** 转 **byte** 操作相反，并使用 **|=** 进行数据位拼接；

**注意：**

在进行转换的时候需要考虑到低位和高位的问题，示例代码从 **int** 数据的低位开始取数，所以 **QByteArray** 中的数据的第一位是 **int** 的最低位，关于从低位到高位，还是高位到地位，需要和调用方商量好。

## 浮点型 <==> QByteArray

**以x86平台的float型为例，float占4位，转换后QByteArray大小应该为4，QByteArray大小决定了存储的数据的范围**

数据传输中，可以将浮点型乘以一定的倍数，转换成整型传输，也可以不乘，这里使用直接转换的方法

关于浮点数在内存中的存储方式，请参考相关文章，这里不作详细描述，总而言之，浮点数和整型数在内存中是相似的，所以只要将浮点数的内存中的内容直接转换即可

```c++
QByteArray Float2Byte(float data, int bits)
{
    QByteArray byte_data;
    bits = (bits > 4 || bits <= 0)? 4: bits;//1/2/3/4

    char* data_char = (char*)&data;
    for(int index = bits - 1; index >= 0; index--)
    {
        byte_data.append(data_char[index]);
    }
    return byte_data;
}

float Byte2Float(QByteArray byte)
{
    float result = 0;
    int size = byte.size();
    char* data_char = byte.data();
    char* p = (char*)&result;
    for(int index = 0; index < size; index++)
    {
        *(p + index) = *(data_char + size - 1 - index);
    }
    return result;
}
```

**说明：**

- 使用 **char\*** 指针直接指向 **float** 数据的内存地址，并使用指针取出地址内容；
- 同样的，反向转换的时候，还是用指针将数据直接保存进内容中；

**注意：**

这里仍然要考虑高位和低位的问题，示例代码 **float** 转 **QByteArray** 中，从高位开始，所以转换后的**QByteArray** 的第一位是最高位，关于从低位到高位，还是高位到地位，需要和调用方商量好。