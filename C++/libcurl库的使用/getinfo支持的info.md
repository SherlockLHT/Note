## 1、CURLINFO_EFFECTIVE_URL

最后以此使用的URL

```c++
CURLcode curl_easy_getinfo(CURL *handle, CURLINFO_EFFECTIVE_URL, char **urlp);
```

需要注意的是，如果遵循重定向，则最后获得的有效URL可能不是设置的那个

获得的值的内存空间，在 **curl_easy_cleanup()** 之后释放