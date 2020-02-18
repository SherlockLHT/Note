# 一、说明

https.c 内的代码是一个简单的 HTTPS 的 GET 方法示例，代码如下：

```c++
#include <stdio.h>
#include <curl/curl.h>
int main(void)
{
    CURL *curl;
    CURLcode res;
    
    curl_global_init(CURL_GLOBAL_DEFAULT);
    curl = curl_easy_init();
    if(curl) 
    {
        curl_easy_setopt(curl, CURLOPT_URL, "https://example.com/");
        
#ifdef SKIP_PEER_VERIFICATION
        curl_easy_setopt(curl, CURLOPT_SSL_VERIFYPEER, 0L);
#endif
#ifdef SKIP_HOSTNAME_VERIFICATION
        curl_easy_setopt(curl, CURLOPT_SSL_VERIFYHOST, 0L);
#endif
        
        res = curl_easy_perform(curl);
        if(res != CURLE_OK)
            fprintf(stderr, "curl_easy_perform() failed: %s\n", curl_easy_strerror(res));
        
        curl_easy_cleanup(curl);
    }
    curl_global_cleanup();
    return 0;
}
```

# 二、代码解析

上面代码中删除了一些注释，调整了排版，其余未变

1. 先使用 **curl_global_init** 方法初始化 libcurl 库，根据API介绍，该方法应该在程序刚开始，没有其他线程的时候调用。程序结束的时候，调用 **curl_global_cleanup** 方法释放资源；
2. 调用 **curl_easy_init** 方法初始化 easy interface，该若没有全局初始化，该方法内会进行初始化。不需要curl 句柄的使用，调用 **curl_easy_cleanup** 方法释放资源；
3. **curl_easy_setopt** 方法是 libcurl 的核心，支持几百种设置；
## CURLOPT_URL

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_URL, char *URL);
```

表示传递要使用的URL，**curl_easy_setopt()** 方法不会检查该参数值的有效性；
## CURLOPT_SSL_VERIFYPEER

```c++
CURLcode curl_easy_setopt(CURL *handle, CURLOPT_SSL_VERIFYPEER, long verify);
```

表示验证对方SSL证书

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

## CURLOPT_SSL_VERIFYHOST

表示使用证书验证主机

**参数：**

参数是一个long类型数据，指定要验证的内容，默认值为2

当参数为0时，表示无论如何，都会验证成功

当参数为1时，7.66.0之后，和2相同，之前会导致出错，不建议使用

当参数为2时，证书必须表明，其上的服务器就是连接的服务器，否则连接失败，即证书中的服务器名称必须要和连接的URL中的名称相同

服务器的验证一般需要和证书认证一起使用