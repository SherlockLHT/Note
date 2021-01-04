# 1 前言

从涉及网络编程以来，除了第一次学习的时候详细了解了一下windows系统下的socket编程外，其他的时候都是使用Qt框架的库来实现。从来不觉得这样有什么问题，直到一次面试，被问到socket的一些知识点，才发现，以前的基础知识都被抛到九霄云外了。加上Qt的socket库但是因为封装过多，导致效率低下，在主打效率的C++中，显得那么另类。

因此，现在花时间重新复习一下socket在windows/linux下的编程方式和一些知识点，巩固一下。

## 1.1 socket机制

socket可以简单理解为一个可以联通网络上不同计算机程序之间的管道，把一堆数据从管道的A端扔进去，则会从管道的B端冒出来。

管道的端口由两个因素来唯一确认，即机器的IP地址和程序所使用的端口号。端口号就是程序指定的一个数字。例如，木马程序成天在网络上扫描不同的端口号就是为了获取一个可以联通的端口从而进行破坏。比较著名的端口号由http的80端口和ftp的21端口。

socket可以支持数据的发送和接收，它会定义一种称为套接字的变量，发送数据时首先创建套接字，然后使用该套接字的sendto等方法对准某个IP/端口号进行数据发送；接收端也首先创建套接字，然后将套接字绑定到一个IP/端口号上，所有发向此端口的数据会被套接字的recv等函数读出，如同从文件中读取数据一样。

# 2 Windows系统

## 2.1 依赖文件

**windows socket 2.0**

头文件：windocket2.h，C:\Program Files (x86)\Microsoft SDKs\Windows\v7.0A\include\WinSock2.h

头文件：winsock.h，C:\Program Files (x86)\Microsoft SDKs\Windows\v7.0A\Include\WinSock.h

库文件：ws2_32.lib，C:\Program Files (x86)\Microsoft SDKs\Windows\v7.0A\Lib

dll文件：ws2_32.dll，C:\Windows\System32

## 2.2 示例代码

### 2.2.1 服务端

```c++
WORD version = MAKEWORD(2, 2);
WSADATA wsadata;
int error = 0;
error = WSAStartup(version, &wsadata);
if (0 != error)
{
	cout << "init socket fail" << endl;
	return 1;
}
cout << "init socket pass" << endl;

if (LOBYTE(wsadata.wVersion) != 2 || HIBYTE(wsadata.wHighVersion) != 2)
{
	cout << "socket version is not 2" << endl;
	WSACleanup();
	return 1;
}
cout << "socket version is correct" << endl;

SOCKADDR_IN sock_addr;
sock_addr.sin_family = AF_INET;
sock_addr.sin_addr.S_un.S_addr = htonl(INADDR_ANY);
sock_addr.sin_port = htons(8069);

SOCKET server = socket(AF_INET, SOCK_STREAM, 0);
if (bind(server, (SOCKADDR*)&sock_addr, sizeof(SOCKADDR))==SOCKET_ERROR)
{
	cout << "bind socket fail" << endl;
	WSACleanup();
	return 1;
}
cout << "bind socket pass" << endl;

if (listen(server, SOMAXCONN) < 0)
{
	cout << "set listen fail" << endl;
	closesocket(server);
	WSACleanup();
	return 1;
}
cout << "set listen pass" << endl;

SOCKADDR_IN client_addr;
int length = sizeof(SOCKADDR);
SOCKET client_socket = accept(server, (SOCKADDR*)&client_addr, &length);
if (INVALID_SOCKET == client_socket)
{
	cout << "accept fail:" << WSAGetLastError() << endl;
	closesocket(server);
	WSACleanup();
	return 1;
}
cout << "accept client" << endl;

SOCKADDR_IN client_sockaddr = (SOCKADDR_IN)client_addr;
char IPdotdec[20]; // 存放点分十进制IP地址
cout << "client ip  :" << inet_ntop(AF_INET, &client_sockaddr.sin_addr, IPdotdec, 16) << endl;
cout << "client port:" << client_sockaddr.sin_port << endl;

char send_buf[1024] = { 0x00 };
char recv_buf[1024] = { 0x00 };
while (1)
{
	int recv_len = recv(client_socket, recv_buf, 3, 0);
	if (recv_len > 0)
	{
		cout << "recv client message:" << recv_buf << endl;
		memset(recv_buf, 0x00, sizeof(recv_buf));

		int send_len = send(client_socket, "get it, welcome", 20, 0);
		if (send_len < 0)
		{
			cout << "send message to client fail" << endl;
		}
	}
	else
	{
		cout << "client disconnect" << endl;
		break;
	}
}
closesocket(server);
closesocket(client_socket);
WSACleanup();
```

### 2.2.2 客户端

```c++
WORD version = MAKEWORD(2, 2);
WSADATA wsadata;
int error = 0;
error = WSAStartup(version, &wsadata);
if (0 != error)
{
	cout << "init socket fail" << endl;
	return 1;
}
cout << "init socket pass" << endl;

if (LOBYTE(wsadata.wVersion) != 2 || HIBYTE(wsadata.wHighVersion) != 2)
{
	cout << "socket version is not 2" << endl;
	WSACleanup();
	return 1;
}
cout << "socket version is correct" << endl;

SOCKET client_socket = socket(AF_INET, SOCK_STREAM, 0);
if (INVALID_SOCKET == client_socket)
{
	cout << "create client socket fail" << endl;
	WSACleanup();
	return 1;
}
SOCKADDR_IN sock_addr;
sock_addr.sin_family = AF_INET;
inet_pton(AF_INET, IP_ADDR, &sock_addr.sin_addr);
sock_addr.sin_port = htons(8069);

error = connect(client_socket, (SOCKADDR*)&sock_addr, sizeof(SOCKADDR));
if (SOCKET_ERROR == error)
{
	cout << "connect to server fail:" << WSAGetLastError() << endl;
	closesocket(client_socket);
	WSACleanup();
	return 1;
}

char* message = "hello server";
int send_len = send(client_socket, message, strlen(message), 0);
if (send_len <= 0)
{
	cout << "send message to server fail:" << WSAGetLastError();
	closesocket(client_socket);
	WSACleanup();
	return 1;
}

while (1)
{
	char recv_buff[1024] = { 0x00 };
	int recv_len = recv(client_socket, recv_buff, 1024, 0);
	if (recv_len > 0)
	{
		cout << "receive server message:" << recv_buff << endl;
	}
	else
	{
		cout << "service disconnect socket" << endl;
		break;
	}
}

closesocket(client_socket);
WSACleanup();
```

### 2.2.3 代码详解：

#### 1、SOCKET类型

SOCKET是socket套接字类型

```c++
typedef _W64 unsigned int UINT_PTR, *PUINT_PTR;
typedef UINT_PTR        SOCKET;
```

套接字就是一个unsigned int型，其被socket环境管理和使用。

套接字将被创建、设置、用来发送和接受数据，最后被关闭。

#### 2、WORD、MAKEWORD、LOBYTE、HIBYTE

WORD类型是一个16位的无符号整型，typedef unsigned short      WORD;

目的：提供两个字节的存储，在socket中这两个字节可以表示主版本号和副版本号。

使用MAKEWORD宏可以给一个WORD类型赋值。例如，表示主版本号2，副版本号0，可以使用如下代码：

```c++
WORD wVersionRequested = MAKEWORD(2,0);
```

注：低位内存存储注版本号2，低位内存副版本号0，其值为0x0002.

使用LOBYTE和HIBYTE宏可以读取WORD的低位和高位字节。

####3、WSADATA类型和LPWSADATA类型

WSADATA类型是一个结构，描述socket库的一些相关信息，结构定义如下：

```c++
typedef struct WSAData {
    WORD               wVersion;		//存储了socket的版本类型
    WORD               wHighVersion;
    char               szDescription[WSADESCRIPTION_LEN+1];
    char			      zSystemStatus[WSASYS_STATUS_LEN+1];
    unsigned short    iMaxSockets;
    unsigned short    iMaxUdpDg;
    char FAR *        lpVendorInfo;
} WSADATA, FAR * LPWSADATA;
```

#### 4、WSAStartup()

作用：初始化socket环境

```c++
int PASCAL FAR WSAStartup ( WORD wVersionRequired , LPWSADATA lpWSAData );
```

PASCAL：调用方式，即标准类型，PASCAL 等于__stdcall

wVersionRequired指明了socket版本号

#### 5、WSACleanup()

socket退出函数

#### 6、socket()

socket的创建函数

```c++
SOCKET PASCAL FAR socket ( int af , int type , int protocol ) ;
```

af：代表网络地址族，目前只有一种取值是有效的，即AF_INET表示internet地址族

type：代表网络协议类型，SOCK_DGRAM代表UDP协议（数据报式），SOCK_STREAM代表TCP协议（流

式）

protocol：网络地址族的特殊协议，目前无用，0即可

返回SOCKET，若返回INVALID_SOCKET则失败

注：关于PF_INET和AF_INET

windows中：

\#define PF_INET         AF_INET

在Linux和Unix中有差别

#### 7、setsocketopt()

设置socket的属性，若不能正确设置socket属性，则数据的发送和接受会失败

```c++
int PASCAL FAR setsockopt (
                __in SOCKET s,
                __in int level,
                __in int optname,
                __in_bcount_opt(optlen) const char FAR * optval,
                __in int optlen);
```

**SOCKET s**：需要设置的套接字

**int level**：代表设置的属性所处的层次，层次包含以下：

```c++
SOL_SOCKET	：套接字层次
IPPROTO_TCP	：TCP协议层次
IPPROTO_IP	：IP协议层次
```

**int optname**：设置参数的名称，SO_BROADCAST代表允许发送广播数据的属性，其他可参考MSDN

**const char FAR* optval**：指向存储参数数值的指针，注意可能要使用reinterpret_cast类型转换

**int optlen**：春初参数数值变量的长度

#### 8、sockaddr_in、in_addr、inet_addr()、inet_ntoa()

sockaddr_in定义了socket发送和接受数据报的地址

```c++
struct sockaddr_in {
	short   sin_family;			//网络地址族，暂时只能取AF_INET
	u_short sin_port;			//IP地址端口，程序员指定
	struct  in_addr sin_addr;	//IP地址
	char    sin_zero[8];		//为了保证sockaddr_in和SOCKADDR类型的长度相等而填充进来的
};
```

```c++
typedef struct in_addr {
	union {
		struct { UCHAR s_b1,s_b2,s_b3,s_b4; } S_un_b;
		struct { USHORT s_w1,s_w2; } S_un_w;
		ULONG S_addr;
	} S_un;
	...
} IN_ADDR, *PIN_ADDR, FAR *LPIN_ADDR;
```

**in_addr：** 存储IP地址的union

有三种表达方式：

1. 用四个字节表示IP地址；

2. 用两个双字节表示IP地址；

3. 用一个长整型表示IP地址；

最简单的IP赋值：inet_addr()函数(字符串==>in_addr类型)：

addrTo.sin_addr.S_un.Saddr = inet_addr(172.168.0.2);

inet_ntoa()：in_addr==>字符串

示例，端口号为9876的一个地址：

```c++
sockaddr_in addrTo;								//发往的地址
memset(&addrTo,0x0,sizeof(addrTo));
addrTo.sin_family = AF_INET;					//地址类型为internetwork
addrTo.sin_addr.S_un.S_addr = INADDR_BROADCAST;	//设置IP为广播地址
addtTo.sin_port = htons(9876);					//端口号为9876
```

#### 9、sockaddr类型

socket地址的类型

相比于sockaddr_in,sockaddr的使用范围更广，因为sockaddr_in只使用于TCP/IP地址。

sockaddr和sockaddr_in所占字节数相同，所以可以相互强制类型转换，事实上也是这么做的。

```c++
struct sockaddr {
	u_short sa_family;              /* address family */
	char    sa_data[14];            /* up to 14 bytes of direct address */
};
```

#### 10、sendto()

成功返回实际发送的字节数，失败返回SOCKET_ERROR

```c++
int PASCAL FAR sendto (
	__in SOCKET s,		//发送者的套接字
	__in_bcount(len) const char FAR * buf,	//发送的内容，指针
	__in int len,			//数据长度，字节数
	__in int flags,			//传送方式的标识，无特殊需要就为0
	__in_bcount_opt(tolen) const struct sockaddr FAR *to,//目标地址，使用sockaddr的指针
	__in int tolen);		//地址长度
```

#### 11、WSAGetLastERROR()

同GetLasrError()

#### 12、closesocket()

关闭套接字，释放资源

##　2.2.3 总结

### （1）TCP服务端顺序

1. 初始化socket环境，创建socket套接字；

2. bind()绑定套接字到本地IP；

3. listen()监听sock套接字；

4. accept()接受客户端的连接，返回客户端的套接字；

5. send()发送数据给客户端的套接字；

6. closesocket()关闭服务端和客户端的套接字，关闭socket环境。

### （2）TCP客户端顺序

1. 初始化socket环境，创建socket套接字；

2. 创建窒息那个服务器的远程地址；

3. connect()连接服务器，使用远程地址；

4. 在套接字上使用recv()接收数据；

5. closesocket()关闭socket套接字，释放socket资源。

# 3 Linux系统

相比于windows系统，linux下的socket编程会简单一些，大致流程也差不多

## 3.1 示例代码

### （1）服务端

```c++
int port = 8080;
int socketFd = socket(AF_INET, SOCK_STREAM, 0);
if (socketFd == -1) {
    cout << "socket init fail, error:" << strerror(socketFd) << endl;
    return 1;
}

struct sockaddr_in addr;
memset(&addr, 0, sizeof(addr));
addr.sin_family = AF_INET;
addr.sin_port = htons(port);//must use htons
addr.sin_addr.s_addr = htonl(INADDR_ANY);

int res = bind(socketFd, (struct sockaddr *) &addr, sizeof(addr));
if (-1 == res) {
    cout << "bind fail," << strerror(socketFd) << endl;
    return 1;
}

res = listen(socketFd, 1);
if (-1 == res) {
    cout << "listen fail," << strerror(socketFd) << endl;
    close(socketFd);
    return 1;
}
int clientSocket = accept(socketFd, (struct sockaddr *) NULL, NULL);
if (-1 == clientSocket) {
    cout << "accept client fail," << strerror(socketFd) << endl;
    close(socketFd);
    return 1;
}

struct sockaddr_in clientAddr;
socklen_t len = sizeof(clientAddr);
res = getsockname(clientSocket, (struct sockaddr *)&clientAddr, &len);
if(0 != res){
    cout<<"get sock name fail"<<endl;
    close(socketFd);
    return 1;
}

cout<<"accept client,"
    <<inet_ntoa(clientAddr.sin_addr)
    <<":"<<ntohs(clientAddr.sin_port)<<endl;

char buffer[2048] = {0x00};
int length = recv(clientSocket, buffer, sizeof(buffer), 0);
if (-1 == length) {
    cout << "recv client message fail," << strerror(clientSocket) << endl;
    close(socketFd);
    return 1;
}
cout << buffer << endl;
memset(buffer, 0x00, sizeof(buffer));

close(socketFd);
```

### （2）客户端

```c++
char *ipAddr = "192.168.2.189";
int port = 8080;

int socketFd = socket(AF_INET, SOCK_STREAM, 0);
if (socketFd == -1) {
    cout << "socket init fail, error:" << strerror(socketFd) << endl;
    return 1;
}

struct sockaddr_in addr;
memset(&addr, 0, sizeof(addr));
addr.sin_family = AF_INET;
addr.sin_port = htons(port);//must use htons
inet_pton(AF_INET, ipAddr, &addr.sin_addr);

int res = connect(socketFd, (struct sockaddr *) &addr, sizeof(addr));
if (-1 == res) {
    cout << "connect fail:" << strerror(socketFd) << endl;
    return 1;
}
cout << "connect server pass"<<endl;

char buffer[2048] = {0x00};
while (true) {
	int recLength = recv(socketFd, buffer, 2048, 0);
	if (-1 == recLength) {
    	cout << "receive message fail:" << strerror(socketFd) << endl;
    	break;
	}
	cout << buffer << endl;
	if(strncmp(buffer, "exit", 4) == 0){
   		break;
	}
	memset(buffer, 0x00, sizeof(buffer));
	string sendMessage = "recv ok";
    int sendLength = send(socketFd, sendMessage.c_str(), sendMessage.length(), 0);
    if(-1 == sendLength){
    cout<<"send fail,error:"<<strerror(socketFd)<<endl;
        break;
    }
}
close(socketFd);
```

## 3.2 说明

从代码中可以看出，linux下的socket的流程和windows下的几乎一样，只是少了初始化socket的步骤，反而简单了很多

# 4 部分方法说明

## 4.1 recv()和read()

read() 和 recv() 方法类似，都是用来接收数据的（不仅限于socket，读文件也可用），区别在于recv() 方法比 read() 方法多了一个flag 参数，用于表示如何来接收数据

两个方法的声明如下：

```c++
ssize_t recv(int sockfd, void *buf, size_t nbytes, int flags);
ssize_t read(inf sockfd, void *buf, size_t nbytes);
```

对于 SOCK_STREAM 模式的套接字来说，recv() 接收到的数据可以比预期的少，因为缓存区并没有那么多数据可供获取。而当使用 flags 为 MSG_WAITALL 后，只有读到预期数据量的数据才会返回。

当然，**MSG_WAITALL** 标志位也不是绝对的，在有中断的情况下还是会返回的，比如：

1. 读到了指定数量的字节；
2. 未读到指定数量的字节，但是读到了文件的结尾；
3. 当操作发生错误时，此时返回-1；

同样的，send() 和 write() 也是相同的区别，这里的标志位可以是0，或者以下的一种或者多种的组合，**MSG_DONTROUTE/MSG_OOB/MSG_PEEK/MSG_WAITALL**

# 5 阻塞和非阻塞

上面的示例代码都是阻塞模式下的socket通信，在实际开发中，结合多线程，使用非阻塞模式会大大提高效率

## 5.1 客户端设置非阻塞模式

客户端设置非阻塞模式有两种方法：

**法一：**

connect() 成功之后，使用那个 fcntl() 方法设置非阻塞模式即可

```c++
int flags = fcntl(socketFd, F_GETFL);
flags |= O_NONBLOCK;
res = fcntl(socketFd, F_SETFL, flags);
if(-1 == res){
    cout<<"set no block fail"<<endl;
}
//...其他代码
int len = recv(socketFd, buffer, sizeof(buffer), 0);
if(len > 0){
	cout<<"recv:"<<buffer<<endl;
}
```

上面示例代码中，先获取socket原来的标志位，在加上非阻塞模式的标志位，使用此方法后，该socket永久性的变成非阻塞模式

**法二：**

recv等方法设置标志位，临时性改成非阻塞模式，原socket依旧是原来的模式

```c++
int len = recv(socketFd, buffer, sizeof(buffer), MSG_DONTWAIT);
if(len > 0){
	cout<<"recv:"<<buffer<<endl;
}
```

## 5.2 服务端设置非阻塞模式

使用 **accept4()** 方法即可，**accept4()** 方法和 **accept()** 方法的区别在于，其多个一个标志位参数，我们在使用时，设置标志位为非阻塞模式即可

```c++
int clientSocket = accept4(socketFd, (struct sockaddr *) NULL, NULL, SOCK_NONBLOCK);
```

