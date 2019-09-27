## http请求

> The QNetworkAccessManager class allows the application to send network requests and receive replies

​	Qt中可以使用 **QNetworkAccessManager** 来发送网络请求和获取返回值

### （1）get请求

```c++
QString url = "https://www.so.com/s?q=1";//使用360搜索1
QNetworkAccessManager manager;
QNetworkReply *reply = manager.get(QNetworkRequest(url_file));
QObject::connect(reply, SIGNAL(finished()), &loop, SLOT(quit()));
loop.exec();//使用事件循环阻塞，知道请求结束或者超时
QString temp = reply->readAll();//得到html数据
```

### (2) post请求

调用 ***post()*** 方法即可