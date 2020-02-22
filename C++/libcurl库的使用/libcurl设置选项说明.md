# 1、CURLOPT_URL

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_URL, char *URL);
```

表示传递要使用的URL，**curl_easy_setopt()** 方法不会检查该参数值的有效性；

#  2、CURLOPT_SSL_VERIFYPEER

表示验证对方SSL证书

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_SSL_VERIFYPEER, long verify);
```

**参数：**

参数值是一个 long 类型数据，默认是1，表示需要进行验证，0表示不需要

当参数值为1时，表示开启验证。如果证书认证失败，则连接失败。

当参数值为0时，证书的认证无论如何都是成功的。

**返回值：**

**CURLE_OK** 表示支持该选项

当使用 TLS 或者 SSL 连接时，服务器将发送一个证书表明身份，curl 会验证证书的真实性，即验证服务器是否是证书中所表明的身份。这个机制基于一系列的数字签名，这些签名会在证书中，且证书是由特定的机构（CA）颁发。curl使用默认的CA证书包（证书包路径在构建时确定），当然证书包路径可以修改，使用 **CURLOPT_CAINFO** 或者 **CURLOPT_CAPATH** 选项指定。

认证服务器光靠认证证书是不够的，还需要验证主机，使用 **CURLOPT_SSL_VERIFYHOST** 选项可以检查证书中的主机名和所连接的主机，以确定服务器是否有效，该选项和 **CURLOPT_SSL_VERIFYPEER** 没有关联。

需要注意的是，禁用证书验证，可能导致通信被别人拦截，使得通信不安全。虽然说通信过程中是有加密的，但是加密是不够的，因为加密不能确保和正确的端点进行通信。

另外，即是禁用此设置，根据使用的 TLS 协议，curl 仍然可能加载指定的证书。而在某些发行版中，curl 的默认设置可能会使用一个很大的文件作为证书，但是加载文件耗费资源，尤其是在处理很多连接的情况下。所以，在某些情况下，可以通过将 **CURLOPT_CAINFO** 设置为NULL来完全禁用验证以节省资源，但缺点是安全问题。

# 3、CURLOPT_SSL_VERIFYHOST

表示使用证书验证主机

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_SSL_VERIFYHOST, long verify);
```

**参数：**

参数是一个long类型数据，指定要验证的内容，默认值为2

当参数为0时，表示无论如何，都会验证成功

当参数为1时，7.66.0之后，和2相同，之前会导致出错，不建议使用

当参数为2时，证书必须表明，其上的服务器就是连接的服务器，否则连接失败，即证书中的服务器名称必须要和连接的URL中的名称相同

服务器的验证一般需要和证书认证一起使用

**返回值：**

**CURLE_OK** 表示支持TLS

**CURLE_UNKNOWN_OPTION** 表示不支持

如果值设置为1，则返回 **CURLE_BAD_FUNCTION_ARGUMENT**

# 4、CURLOPT_CAINFO

设置CA（Certificate Authority）证书包路径

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_CAINFO, char *path);
```

该选项默认在建立libcurl的cacert软件包存储的系统路径下

**返回值：**

CURLE_OK 表示支持该选项

CURLE_UNKNOWN_OPTION 表示不支持

CURLE_OUT_OF_MEMORY 表示堆空间不足

# 5、CURLOPT_FOLLOWLOCATION

遵循重定向

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_FOLLOWLOCATION, long enable);
```

**参数：**

默认0，表示禁用重定向

当参数设置为1时，则curl库会遵循服务器在3xx 的 response 中的重定向，libcurl将会再次发出对新的url的请求，直到此类重定向不再出现为止

libcurl 也可以设置重定向的次数，使用 **CURLOPT_MAX_REDIRS** 配置即可

# 6、CURLOPT_MAX_REDIRS

允许重定向的最大次数

```c++
CURLcode curl_easy_setopt(CURL*handle, CURLOPT_MAXREDIRS, long amount)
```

当 **CURLOPT_FOLLOWLOCATION** 开启时，此项才有意义

**参数：**

设置为0，表示拒绝重定向

设置为-1，表示无限次重定向

当实际重定向的次数超出限定的次数，则下以此重定向会导致 **CURLE_TOO_MANY_REDIRECTS** 的错误

**返回值：**

CURLE_OK 表示支持HTTP

**CURLE_UNKNOWN_OPTION** 表示不支持

# 7、CURLOPT_POST

接下来的perform使用POST方法进行HTTP request

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POST, long post);
```

设置并请求之后，如果需要使用其他方法发送请求，则需要设置成对应的方法，否则，同一个句柄都会使用POST方法

**参数：**

设置为1表示，进行常规的HTTP请求

默认为0，表示不使用POST方法

当此参数设置为1时，libcurl会自动将 CURLOPT_NOBODY 和 CURLOPT_HTTPGET 选项设置为0

# 8、CURLOPT_POSTFIELDS

POST 方法中要传递给服务器的完整数据

```c++
CURLcode curl_easy_setopt(CURL* handle, CURLOPT_POSTFIELDS, char* postdata);
```

传递的数据要以服务器能处理的格式进行格式化，libcurl不会以任何方法转换或者编码

使用此选项即表示，**CURLOPT_POST** 选项自动设置为1

# 9、CURLOPT_POSTFIELDSIZE

POST的数据的大小

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POSTFIELDSIZE, long size);
```

**参数：**

设置为-1，则 libcurl 将会使用 strlen() 方法计算size

如果数据size超过2G，则使用 **CURLOPT_POSTFIELDSIZE_LARGE**

**返回值：**

CURLE_OK 表示支持HTTP

CURLE_UNKNOWN_OPTION 表示不支持

# 10、CURLOPT_POSTFIELDSIZE_LARGE

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POSTFIELDSIZE_LARGE, curl_off_t size);
```

作用同 **CURLOPT_POSTFIELDSIZE**， 当post的数据超过2G时，使用此选项

# 11、CURLOPT_WRITEFUNCTION

用于设置接收数据的回调函数

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_WRITEFUNCTION, write_callback);
```

回调函数声明如下：

```c++
size_t write_callback(char *ptr, size_t size, size_t nmemb, void *userdata);
```

设置回调函数之后，一旦接收到 response，就会立即执行此回调方法

**回调函数参数：**

ptr： 数据指针，指向接收到的 response 数据

size：永远是1

nmemb：数据的size

userdata：用于使用 CURLOPT_WRITEDATA 传递进来的自定义指针

回调函数会尽可能多地传递数据，但并不是全部数据，有可能是一个字节，也可能是数千字节。传递的的最大字节数在头文件 **curl.h** 中使用 ***CURL_MAX_WRITE_SIZE*** 定义，默认是16K，

# 12、CURLOPT_WRITEDATA

传递给回调函数的用户自定义指针，用户指针可以是各种类型，传递的时候需要转换成 **void\***

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_WRITEDATA, void *pointer);
```

如果使用 **CURLOPT_WRITEFUNCTION** 选项，则此指针则会是回调函数的第四个参数；

如果没有使用 **CURLOPT_WRITEFUNCTION** 选项，则此指针必须是 **FILE\*** ，因为libcurl在写数据的时候会将此指针传递给 **fwrite**

在 windows 平台下，此选项必须和 **CURLOPT_WRITEFUNCTION** 一起使用，否则会崩溃

**默认：**

默认是一个 **FILE\***  指向stdout，即会在控制台输出数据

