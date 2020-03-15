# 1、URL

CURLOPT_URL

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_URL, char *URL);
```

表示传递要使用的URL，**curl_easy_setopt()** 方法不会检查该参数值的有效性；

#  2、验证对方SSL证书

CURLOPT_SSL_VERIFYPEER

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

# 3、使用证书验证主机

CURLOPT_SSL_VERIFYHOST

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

# 4、设置CA证书包路径

CURLOPT_CAINFO

设置CA（Certificate Authority）证书包路径

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_CAINFO, char *path);
```

该选项默认在建立libcurl的cacert软件包存储的系统路径下

**返回值：**

CURLE_OK 表示支持该选项

CURLE_UNKNOWN_OPTION 表示不支持

CURLE_OUT_OF_MEMORY 表示堆空间不足

# 5、遵循重定向

CURLOPT_FOLLOWLOCATION

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_FOLLOWLOCATION, long enable);
```

**参数：**

默认0，表示禁用重定向

当参数设置为1时，则curl库会遵循服务器在3xx 的 response 中的重定向，libcurl将会再次发出对新的url的请求，直到此类重定向不再出现为止

libcurl 也可以设置重定向的次数，使用 **CURLOPT_MAX_REDIRS** 配置即可

# 6、允许重定向的最大次数

CURLOPT_MAXREDIRS

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

# 7、使用POST方法

CURLOPT_POST

接下来的perform使用POST方法进行HTTP request

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POST, long post);
```

设置并请求之后，如果需要使用其他方法发送请求，则需要设置成对应的方法，否则，同一个句柄都会使用POST方法

**参数：**

设置为1表示，进行常规的HTTP请求

默认为0，表示不使用POST方法

当此参数设置为1时，libcurl会自动将 CURLOPT_NOBODY 和 CURLOPT_HTTPGET 选项设置为0

# 8、POST 方法传递数据

CURLOPT_POSTFIELDS

POST 方法中要传递给服务器的完整数据

```c++
CURLcode curl_easy_setopt(CURL* handle, CURLOPT_POSTFIELDS, char* postdata);
```

传递的数据要以服务器能处理的格式进行格式化，libcurl不会以任何方法转换或者编码

使用此选项即表示，**CURLOPT_POST** 选项自动设置为1

# 9、POST的数据的大小

CURLOPT_POSTFIELDSIZE

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POSTFIELDSIZE, long size);
```

**参数：**

设置为-1，则 libcurl 将会使用 strlen() 方法计算size

如果数据size超过2G，则使用 **CURLOPT_POSTFIELDSIZE_LARGE**

**返回值：**

CURLE_OK 表示支持HTTP

CURLE_UNKNOWN_OPTION 表示不支持

# 10、POST的数据的大小-大于2G

CURLOPT_POSTFIELDSIZE_LARGE

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_POSTFIELDSIZE_LARGE, curl_off_t size);
```

作用同 **CURLOPT_POSTFIELDSIZE**， 当post的数据超过2G时，使用此选项

# 11、设置接收数据的回调函数

CURLOPT_WRITEFUNCTION

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

# 12、传递给回调函数的数据指针

CURLOPT_WRITEDATA

传递给回调函数的用户自定义指针，用户指针可以是各种类型，传递的时候需要转换成 **void\***

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_WRITEDATA, void *pointer);
```

如果使用 **CURLOPT_WRITEFUNCTION** 选项，则此指针则会是回调函数的第四个参数；

如果没有使用 **CURLOPT_WRITEFUNCTION** 选项，则此指针必须是 **FILE\*** ，因为libcurl在写数据的时候会将此指针传递给 **fwrite**

在 windows 平台下，此选项必须和 **CURLOPT_WRITEFUNCTION** 一起使用，否则会崩溃

**默认：**

默认是一个 **FILE\***  指向stdout，即会在控制台输出数据

# 13、不获取body进行request

CURLOPT_NOBODY

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_NOBODY, long opt);
```

设置为1时，表示请求之后，不获取body，即数据流中没有Body内容

默认为0

# 14、将标头传递到数据流中

CURLOPT_HEADER

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_HEADER, long onoff);
```

设置为1时，使用 **CURLOPT_WRITEFUNCTION** 设置的回调函数的数据流中会有标头和其他原数据，具体内容和协议有关

但是，**CURLOPT_WRITEFUNCTION** 设置的回调函数，最多能获取 *CURL_MAX_WRITE_SIZE* （16KB）的数据，而header可能很长从而超出此size，所以，不建议使用此设置获取header

可以使用 **CURLOPT_HEADERFUNCTION** 设置获取标头的回调函数，以获取标头，最多支持 *CURL_MAX_HTTP_HEADER*（100KB）的数据传递

所以，最好分别获取header和body数据

# 15、设置获取header的回调函数

CURLOPT_HEADERFUNCTION

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_HEADERFUNCTION, header_callback);
```

当接受到标头数据后，就会立即调用回调函数。每个完整的标头行都会执行一次回调

**回调函数：**

```c++
size_t header_callback(char *buffer, size_t size, size_t nitems, void *userdata);
```

buffer 指向所传递的数据

size 始终为1

nitems 表示该数据的size

userdata 为传递的用户指针，使用 **CURLOPT_HEADERDATA** 设置

需要注意的是，不能假设标头行以0结尾

回调函数必须返回实际处理的字节数，如果返回的字节数和传递给函数的数量不同，则会想库发出错误信号，这将导致传输异常终止，正在进行的 libcurl 函数将返回 **CURLE_WRITE_ERROR**

传递给回调函数的标头最多可以为 *CURL_MAX_HTTP_HEADER* （100K）字节，并包括最后的行终止符

另外，传递给回调函数的数据不仅仅包括response的header，也有在身份验证期间发生的response，所以，如果想要获取最终的response的header，则需要在回调函数中使用HTTP状态行自己处理。

对于HTTP传输，响应行之前的状态行和空白行均作为标头包含在内，并传递给此函数

**支持协议：**

所有带有header和元数据概念的协议：HTTP、FTP、POP3、IMAP、SMTP等

# 16、传递给标头回调函数的数据指针

CURLOPT_HEADERDATA

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_HEADERDATA, void *pointer);
```

**CURLOPT_HEADERFUNCTION** 设置的回调函数会将此指针传递到回调函数中，如果没有设置，但是设置了 **CURLOPT_WRITEFUNCTION** 的回调函数，则此指针则会传递到此函数中

也可以不设置回调函数，而直接传递 **FILE\*** 文件指针，则有数据的时候，会调用 fwrite() 方法处理数据

# 17、设置自定义的header

CURLOPT_HTTPHEADER

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_HTTPHEADER, struct curl_slist *headers);
```

第三个参数需要传递 **curl_slist** 指针，curl_slist 指针需要使用 **curl_slist_append()** 方法创建，最后使用 **curl_slist_free_all()** 方法清理列表

使用此设置可以添加、替换或者删除新的header项（设置的header想为空则为删除）

如果要添加不包含任何内容的header，则使用分号结尾，示例：

```c++
header = curl_slist_append(header,"Accept;");
```

libcurl 会在每个header后面加上CRLF，所以，设置的header不能添加CRLF，不然会导致奇怪的错误，因为服务器可能会忽略指定的header

需要注意的是，当使用 **curl_easy_setopt()** 方法设置header之后，libcurl不会复制此 header list，因为此header list 不能释放，只有当使用完成之后才能释放

传递 NULL 给此项，则表示重置为无自定义header

**使用示例：**

```c++
curl_slist *header = NULL;
header = curl_slist_append(header,"Accept:*/*");
//header = curl_slist_append(header,"Accept:");	//设置为空则表示此项禁用
header = curl_slist_append(header,"User-Agent:Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1)");
header = curl_slist_append(header,"Connection:keep-alive");

curl_easy_setopt(curlHandle, CURLOPT_URL, url);
curl_easy_setopt(curlHandle, CURLOPT_HTTPHEADER, header);//设置header

curl_slist_free_all(header);	//清理curl_slit资源
```

最经常替换的header有 **CURLOPT_COOKIE** 、**CURLOPT_USERAGENT** 和 **CURLOPT_REFERER**

**安全问题：**

设置header后，如果遵循了重定向，则设置的header会发送到新的服务器

从7.58.0开始，libcurl除非使用 **CURLOPT_UNRESTRICTED_AUTH** 明确允许，否则，将阻止 **Authorization** 头发送到第一个以外的主机

从7.64.0开始，libcurl除非使用 **CURLOPT_UNRESTRICTED_AUTH** 明确允许，否则，将阻止 **Cookie** 头发送到第一个以外的主机