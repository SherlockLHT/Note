**select** 用于监听IO文件描述符的属性变化，它可以监听可读、可写、异常三种属性的变化

以下为socket server编程中的select示例，在一个进程中和多个客户端通信

```c++
#include <netinet/in.h>
#include <sys/socket.h>
#include <sys/select.h>
#include <unistd.h>
#include <string.h>
#include <strings.h>
#include <iostream>
#include <fcntl.h>

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

    fd_set readFdSet, writeFdSet, exceptFdSet;
    int maxSocket;

    maxSocket = serverFd;
    int clientSocketFd[5] = {0x00};//存放活动的客户端socket数组

    struct timeval timeout;
    timeout.tv_usec = 0;
    timeout.tv_sec = 0;

    int connectCount = 0;
    char buffer[1024] = {0x00};

    FD_ZERO(&readFdSet);
    FD_ZERO(&exceptFdSet);
    FD_ZERO(&writeFdSet);

    while (true) {
        struct sockaddr_in clientAddr;
        socklen_t len = sizeof(struct sockaddr_in);
        int clientSocket = accept(serverFd, (struct sockaddr *) (&clientAddr), &len);
        if (clientSocket > 0) {
            if (connectCount < 5) {
                if (clientSocket > maxSocket) {
                    maxSocket = clientSocket;
                }
                clientSocketFd[connectCount++] = clientSocket;
                char sendBuffer[64] = "welcome to connect";
                send(clientSocket, sendBuffer, sizeof(sendBuffer), 0);
                cout << "new connect " << clientSocket << endl;
            } else {
                cout << "max connect, quit" << endl;
                break;
            }
        }

        for (int index = 0; index < 5; index++) {
            if (clientSocketFd[index] != 0) {
                FD_SET(clientSocketFd[index], &readFdSet);
                FD_SET(clientSocketFd[index], &exceptFdSet);
            }
        }
        res = select(maxSocket + 1, &readFdSet, &writeFdSet, &exceptFdSet, &timeout);
        if (res < 0) {
            cout << "select fail" << endl;
            break;
        } else if (0 == res) {
            continue;
        }
        for (int index = 0; index < 5; index++) {
            int clientSocket = clientSocketFd[index];
            if (FD_ISSET(clientSocket, &readFdSet)) {
                cout << "start recv from socket[" << clientSocket << "]" << endl;
                memset(buffer, 0x00, sizeof(buffer));
                res = recv(clientSocket, buffer, 1024, MSG_DONTWAIT);
                if (res <= 0) {
                    cout << "client " << index << " recv fail" << endl;
                    FD_CLR(clientSocket, &readFdSet);
                    clientSocketFd[index] = 0;
                } else {
                    cout << "client " << index << " recv:" << buffer << endl;
                }
            }
        }
    }

    for (int index = 0; index < 5; index++) {
        if (clientSocketFd[index] != 0) {
            close(clientSocketFd[index]);
        }
    }
    close(serverFd);
    return 0;
}
```

## 代码说明

开始的一大段都是socket的初始化、开始监听以及非阻塞accept的代码，忽略

```c++
FD_ZERO(&readFdSet);
FD_ZERO(&exceptFdSet);
FD_ZERO(&writeFdSet);
```

**FD_ZERO** 用于清空描述符set

```c++
if (connectCount < 5) {
    if (clientSocket > maxSocket) {
        maxSocket = clientSocket;
    }
    clientSocketFd[connectCount++] = clientSocket;
    char sendBuffer[64] = "welcome to connect";
    send(clientSocket, sendBuffer, sizeof(sendBuffer), 0);
    cout << "new connect " << clientSocket << endl;
} else {
    cout << "max connect, quit" << endl;
    break;
}
```

这里在accept到客户端之后，先把客户端的描述符保存到数组中。这里因为是demo，所以限制了最多5个客户端的连接

```c++
for (int index = 0; index < 5; index++) {
    if (clientSocketFd[index] != 0) {
        FD_SET(clientSocketFd[index], &readFdSet);
        FD_SET(clientSocketFd[index], &exceptFdSet);
    }
}
```

遍历保存客户端描述符的数组，若有已连接的客户端，则将其描述符添加到 set 中，set 会传给 select，以保证 select 能监听指定的描述符的属性改变。这里只监听可读和异常

需要注意的是，这里在循环中调用 select，每次调用之前都必须执行 **FD_SET**，因为select方法会清空原先的set，添加有属性变化的set

```c++
struct timeval timeout;
timeout.tv_usec = 0;
timeout.tv_sec = 0;

res = select(maxSocket + 1, &readFdSet, &writeFdSet, &exceptFdSet, &timeout);
if (res < 0) {
    cout << "select fail" << endl;
    break;
} else if (0 == res) {
    continue;
}
```

这里的time是0，表示非阻塞

需要注意，这里超时设置为0，表示非阻塞，不是select的参数传递0，若传递0，则表示NULL，为阻塞式

当res小于0，表示出错了，等于0表示没有在指定超时内监听到属性变化，大于0，表示属性变化的描述符的个数

```c++
for (int index = 0; index < 5; index++) {
    int clientSocket = clientSocketFd[index];
    if (FD_ISSET(clientSocket, &readFdSet)) {
        cout << "start recv from socket[" << clientSocket << "]" << endl;
        memset(buffer, 0x00, sizeof(buffer));
        res = recv(clientSocket, buffer, 1024, MSG_DONTWAIT);
        if (res <= 0) {
            cout << "client " << index << " recv fail" << endl;
            FD_CLR(clientSocket, &readFdSet);
            clientSocketFd[index] = 0;
        } else {
            cout << "client " << index << " recv:" << buffer << endl;
        }
    }
}
```

遍历描述符数组，使用 **FD_ISSET** 检查描述符是否在指定的 set 中，若不在，则指定的属性无变化，也可能是没有监听

若有指定属性变化，则进行相关操作，描述符若可读，则读取数据，读取失败则表示连接异常，从描述符列表中移除，当然，读取失败的描述符也会出现在异常的set中

最后，close方法关闭描述符释放资源

