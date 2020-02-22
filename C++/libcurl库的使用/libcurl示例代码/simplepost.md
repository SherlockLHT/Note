# 一、说明

simplepost.c 中的代码是一个简单的post方法使用示例

```c++
#include <curl/curl.h>
int main(void)
{
    CURL *curl;
    CURLcode res;

    static const char *postthis = "moo mooo moo moo";
    curl = curl_easy_init();
    if(curl)
    {
        curl_easy_setopt(curl, CURLOPT_URL, "http://127.0.0.1:8080");
        curl_easy_setopt(curl, CURLOPT_POSTFIELDS, postthis);
        curl_easy_setopt(curl, CURLOPT_POSTFIELDSIZE, (long)strlen(postthis));

        res = curl_easy_perform(curl);
        if(res != CURLE_OK)
            fprintf(stderr, "curl_easy_perform() failed: %s\n", curl_easy_strerror(res));
    }

    curl_easy_cleanup(curl);
    cout<<"Press enter to continue..."<<endl;
    return 0;
}
```

# 二、代码说明

用 **CURLOPT_POSTFIELDS** 设置需要post的数据，**CURLOPT_POSTFIELDSIZE** 设置post的数据的size，如果不设置，则 libcurl  默认使用 strlen() 方法获取大小