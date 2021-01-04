**poll** 代码示例：

```c++
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/select.h>
#include <unistd.h>
#include <string.h>
#include <strings.h>
#include <iostream>
#include <fcntl.h>
#include <poll.h>

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

    while (true) {
        struct sockaddr_in clientAddr;
        socklen_t len = sizeof(struct sockaddr_in);
        int clientSocket = accept(serverFd, (struct sockaddr *) (&clientAddr), &len);
        if (clientSocket > 0) {
            if (connectCount < 5) {
                if (clientSocket > maxSocket) {
                    maxSocket = clientSocket;
                }
                struct pollfd clientPoll;
                clientPoll.fd = clientSocket;
                clientPoll.events = POLLIN | POLLOUT;
                fd[connectCount++] = clientPoll;
                char sendBuffer[64] = "welcome to connect";
                send(clientSocket, sendBuffer, sizeof(sendBuffer), 0);
                cout << "new connect " << clientSocket << endl;
            } else {
                cout << "max connect, quit" << endl;
                break;
            }
        }

        res = poll(fd, 5, 0);
        if (res < 0) {
            cout << "poll fail" << endl;
            break;
        } else if (0 == res) {
            continue;
        }
        for (int index = 0; index < 5; index++) {
            struct pollfd clientPoll = fd[index];
            if(clientPoll.fd <= 0){
                continue;
            }
            if (clientPoll.revents & POLLIN) {
                char buffer[1024] = {0x00};
                res = read(clientPoll.fd, buffer, 1024);
                if(res <= 0){
                    cout<<"diconnect "<<fd[index].fd<<endl;
                    close(fd[index].fd);
                    fd[index].fd = 0;
                } else {
                    cout<<"read socket ["<<clientPoll.fd<<"]"<<" "<<buffer<<endl;
                }
            }
        }
    }
    for (int index = 0; index < 5; index++) {
        struct pollfd clientPoll = fd[index];
        if (clientPoll.fd != 0) {
            close(clientPoll.fd);
        }
    }

    close(serverFd);
    return 0;
}
```

## 代码说明：

开头的socket初始化、监听以及accept忽略

```c++
struct sockaddr_in clientAddr;
socklen_t len = sizeof(struct sockaddr_in);
int clientSocket = accept(serverFd, (struct sockaddr *) (&clientAddr), &len);
if (clientSocket > 0) {
    if (connectCount < 5) {
        if (clientSocket > maxSocket) {
            maxSocket = clientSocket;
        }
        struct pollfd clientPoll;
        clientPoll.fd = clientSocket;
        clientPoll.events = POLLIN | POLLOUT;
        fd[connectCount++] = clientPoll;
        char sendBuffer[64] = "welcome to connect";
        send(clientSocket, sendBuffer, sizeof(sendBuffer), 0);
        cout << "new connect " << clientSocket << endl;
    } else {
        cout << "max connect, quit" << endl;
        break;
    }
}
```

这里主要看其中几行

```c++
struct pollfd clientPoll;
clientPoll.fd = clientSocket;
clientPoll.events = POLLIN | POLLOUT;
fd[connectCount++] = clientPoll;
```

这里接收到客户端连接后，保存描述符，并设置监听事件标志位，后面检查的时候使用 **&** 检查标志位

```c++
struct pollfd fd[5];
for (int index = 0; index < 5; index++) {
    fd[index].fd = 0;
}
```

定义一个 pollfd 数组并初始化，用于存放 poll 监听的描述符和事件

```c++
res = poll(fd, 5, 0);
if (res < 0) {
    cout << "poll fail" << endl;
    break;
} else if (0 == res) {
    continue;
}
```

执行poll方法，5表示传入的pollfd的数量，0表示非阻塞

返回值小于0表示出错，等于0表示超时

```c++
for (int index = 0; index < 5; index++) {
    struct pollfd clientPoll = fd[index];
    if(clientPoll.fd <= 0){
    	continue;
	}
    ...
}
```

遍历每个pollfd，检查事件

```c++
if (clientPoll.revents & POLLIN) {
    char buffer[1024] = {0x00};
    res = read(clientPoll.fd, buffer, 1024);
    if(res <= 0){
        cout<<"diconnect "<<fd[index].fd<<endl;
        close(fd[index].fd);
        fd[index].fd = 0;
    } else {
        cout<<"read socket ["<<clientPoll.fd<<"]"<<" "<<buffer<<endl;
    }
}
```

检查revent的标志位，可读则读取数据