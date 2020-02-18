# 一、说明

simple.c 文件中的代码是一个最简单的GET方法示例

```c++
#include <curl/curl.h>
int main(void)
{
    CURL *curl;
    CURLcode res;

    curl = curl_easy_init();
    if(curl) 
    {
        curl_easy_setopt(curl, CURLOPT_URL, "https://example.com");
        curl_easy_setopt(curl, CURLOPT_FOLLOWLOCATION, 1L);

        res = curl_easy_perform(curl);
        if(res != CURLE_OK)
        {
            fprintf(stderr, "curl_easy_perform() failed: %s\n",curl_easy_strerror(res));
        }
        curl_easy_cleanup(curl);
    }
    cout<<"Press enter to continue..."<<endl;
    return 0;
}
```

# 二、代码说明

出去通用的init以及clean等，只针对setop方法，CURLOPT_URL 不必细说

**CURLOPT_FOLLOWLOCATION** 设置为1表示支持重定向

新建工程测试的时候，出现一个错误：

```c++
SSL peer certificate or SSH remote key was not OK
```

libcurl 是默认开启证书验证的，这个错误就表示，验证证书的时候，发现CA证书不正确，可能是用了错误的证书

在测试网页上查看并下载了证，并使用 **CURLOPT_CAINFO** 参数设置了证书，又报错

```c++
curl_easy_perform() failed: Problem with the SSL CA cert (path? access rights?)
```

因为到处的证书使用DER编译的二进制，难道不支持，然后换成了Base64编码，重新设置证书，测试通过

代码中主要是在perform之前添加一句证书的设置

```c++
curl_easy_setopt(curl, CURLOPT_CAINFO, "./test.cer");
```

