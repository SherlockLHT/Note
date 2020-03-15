<font size = 4>

参考：https://blog.csdn.net/whatday/article/details/97617552

## 概述

Base64  是网络上最常见的用于传输 8Bit 字节码的编码方式之一，Base64 就是一种基于 64 个可打印字符来表示二进制数据的方法。可查看 RFC2045 ～ RFC2049，上面有 MIME 的详细规范。Base64 编码是从二进制到字符的过程，可用于在 HTTP 环境下传递较长的标识信息。比如使二进制数据可以作为电子邮件的内容正确地发送，用作 URL 的一部分，或者作为 HTTP POST 请求的一部分

base64是python3内置模块  import base64 引入即可

## 简单使用

我们最常用的两个方法即b64encode和b64decode-Base64 编码和解码，其中 b64encode 的参数 s 的类型必须是字节包（bytes）。b64decode 的参数 s 可以是字节包（bytes），也可以是字符串（str）。

## Base64 编码

```python
S = b'I like Python'
e64 = base64.b64encode(S)
print(e64)
```

示例结果：

```html
b'SSBsaWtlIFB5dGhvbg=='
```

## Base64 解码

```python
S = 'SSBsaWtlIFB5dGhvbg=='
d64 = base64.b64decode(S)
print(d64)
```

示例结果：

```html
b'I like Python'
```