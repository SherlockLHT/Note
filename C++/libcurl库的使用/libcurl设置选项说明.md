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