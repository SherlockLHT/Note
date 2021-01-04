**epoll** 简单代码demo

```c++
#include <netinet/in.h>
#include <sys/socket.h>
#include <unistd.h>
#include <strings.h>
#include <iostream>
#include <fcntl.h>
#include <poll.h>
#include <sys/epoll.h>

using namespace std;
#define PORT 8080

int main() {

    struct sockaddr_in myAddr;
    int serverFd = socket(AF_INET, SOCK_STREAM, 0);
    if (-1 == serverFd) {
        cout << "init cosket error" << endl;
        return -1;
    }
    int flags = fcntl(serverFd, F_GETFL);
    flags |= O_NONBLOCK;
    fcntl(serverFd, F_SETFL, flags);
    cout << "init socket pass" << endl;
    myAddr.sin_family = AF_INET;
    myAddr.sin_port = htons(PORT);
    myAddr.sin_addr.s_addr = INADDR_ANY;
    int res = bind(serverFd, (struct sockaddr *) &myAddr, sizeof(struct sockaddr));
    if (-1 == res) {
        cout << "bind fail" << endl;
        close(serverFd);
    }
    cout << "bind pass" << endl;

    res = listen(serverFd, 5);
    if (-1 == res) {
        cout << "listen fail" << endl;
        close(serverFd);
        return -1;
    }
    cout << "listen pass" << endl;

    int maxSocket;

    maxSocket = serverFd;

    int connectCount = 0;
    char buffer[1024] = {0x00};
    struct pollfd fd[5];
    for (int index = 0; index < 5; index++) {
        fd[index].fd = 0;
    }

    int epollFd = epoll_create(1);
    if (epollFd < 0) {
        cout << "epoll create fail" << endl;
        close(serverFd);
        return -1;
    }
    cout << "epoll create pass" << endl;

    while (true) {
        struct sockaddr_in clientAddr;
        socklen_t len = sizeof(struct sockaddr_in);
        int clientSocket = accept(serverFd, (struct sockaddr *) (&clientAddr), &len);
        if (clientSocket > 0) {
            if (connectCount < 5) {
                if (clientSocket > maxSocket) {
                    maxSocket = clientSocket;
                }
                struct epoll_event event;
                event.events = EPOLLIN | EPOLLOUT;
                event.data.fd = clientSocket;
                res = epoll_ctl(epollFd, EPOLL_CTL_ADD, clientSocket, &event);
                if (res < 0) {
                    cout << "epoll ctl fail" << endl;
                }
                char sendBuffer[64] = "welcome to connect";
                send(clientSocket, sendBuffer, sizeof(sendBuffer), 0);
                cout << "new connect " << clientSocket << endl;
            } else {
                cout << "max connect, quit" << endl;
                break;
            }
        }
        struct epoll_event events[5];
        for (int index = 0; index < 5; index++) {
            events[index].events = 0;
            events[index].data.fd = 0;
        }
        res = epoll_wait(epollFd, events, 5, 0);
        if (res < 0) {
            cout << "epoll wait fail" << endl;
            break;
        } else if (0 == res) {
            continue;
        }
        for (int index = 0; index < 5; index++) {
            epoll_event epollEvent = events[index];
            if (epollEvent.events & EPOLLIN) {
                char buffer[1024] = {0x00};
                if (read(epollEvent.data.fd, buffer, 1024) > 0) {
                    cout << "recv from socket " << epollEvent.data.fd << ":" << buffer << endl;
                } else {
                    cout << "socket " << epollEvent.data.fd << " except" << endl;
                    if (epoll_ctl(epollFd, EPOLL_CTL_DEL, epollEvent.data.fd, NULL) >= 0) {
                        cout << "delete socket " << epollEvent.data.fd << " pass" << endl;
                    } else {
                        cout << "delete socket " << epollEvent.data.fd << " fail" << endl;
                    }
                }
            }
        }

    }

    close(epollFd);
    close(serverFd);
    return 0;
}
```

## 代码说明：

开始的socket的初始化和bind以及listen忽略不提

```c++
struct epoll_event event;
event.events = EPOLLIN | EPOLLOUT;
event.data.fd = clientSocket;
res = epoll_ctl(epollFd, EPOLL_CTL_ADD, clientSocket, &event);
if (res < 0) {
    cout << "epoll ctl fail" << endl;
}
```

这里是在accept到新的客户端连接后，将客户端的描述符添加到epoll的监听中去，这里监听了读写事件

```c++
struct epoll_event events[5];
for (int index = 0; index < 5; index++) {
    events[index].events = 0;
    events[index].data.fd = 0;
}
res = epoll_wait(epollFd, events, 5, 0);
```

定义了一个epoll事件数组，最多接受5组事件，定义好了之后记得初始化，否则默认数据会有意外错误

然后执行 epoll_wait ，返回值表示事件数量

```c++
for (int index = 0; index < 5; index++) {
    epoll_event epollEvent = events[index];
    if (epollEvent.events & EPOLLIN) {
        char buffer[1024] = {0x00};
        if (read(epollEvent.data.fd, buffer, 1024) > 0) {
            cout << "recv from socket " << epollEvent.data.fd << ":" << buffer << endl;
        } else {
            cout << "socket " << epollEvent.data.fd << " except" << endl;
            if (epoll_ctl(epollFd, EPOLL_CTL_DEL, epollEvent.data.fd, NULL) >= 0) {
                cout << "delete socket " << epollEvent.data.fd << " pass" << endl;
            } else {
                cout << "delete socket " << epollEvent.data.fd << " fail" << endl;
            }
        }
    }
}
```

遍历数组，找到可读的事件，读取客户端消息

```c++
close(epollFd);
```

epoll 使用完了需要close