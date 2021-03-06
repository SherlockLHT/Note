# 一、简介

HTTP（HyperText Transfer Protocol），即超文本传输协议。面向应用层，基于TCP/IP协议，默认端口80。

HTTP协议是一种无状态协议，即客户端和服务端之间是短链接，这样，双方的通信必须是由客户端发起（Request），服务端响应（Response），在接收到客户端的应答之后，断开连接。

**特点：**

1. 简答快速；
2. 灵活：HTTP协议允许传递任意类型，只要客户端和服务端知道如何处理数据即可；
3. 无连接：每次连接只处理一个请求，服务器处理完，收到客户端应答后，断开连接，节省传输时间；
4. 无状态：对事务的处理没有记忆能力，即服务端后续如果还要处理之前的信息，则必须让客户端重新传输，这样可能导致每次阐述的数据量变大；

# 二、URL和URI

URL（Uniform Resource Locator），即统一资源定位符，用来标志某一个资源的地址

URI（Uniform Resource Identifiers），统一资源标志符，表示web上的每一种可用的资源，如HTML、图片、视频等，都是用URI来进行定位

## URL示例

一个URL的格式为：http://host:port/URI

示例URL1：http://192.168.51.120:8069/web#menu_id=2233&action=296

示例URL2：https://www.cnblogs.com/sherlock-lin/p/11705031.html?name=admin&id=12

URL从前到后依次是：

1. 协议部分：**http:** 表示该页面使用HTTP协议，**https:** 表示使用HTTPS协议，后面使用 **//** 作为分隔符；
2. 域名部分：**www.cnblogs.com**表示域名，域名可以是IP地址。www的域名是有机构在管理，需要注册；
3. 端口部分：域名后面是端口，和域名之间用 **:** 隔开。端口并不是必须的，如果省略，则使用默认端口；
4. 虚拟目录：从端口后面开始，虚拟目录以 **/** 开头，以 **/** 结尾，不是必须的；
5. 文件名部分：虚拟目录后面开始，内容是一个文件名称；
6. 锚部分：从文件名后开始，以 **#** 开头，比如示例URL1中的 **menu_id=2233&action=296** 都是锚；
7. 参数部分：从文件名后开始，以 **?** 开头，比如示例URL2中的 **name=admin&id=12** 都是参数；

## URL中的#和?

### 1、#作用

**#** 即锚点，顾名思义，代表网页的一个位置，后面的字符，代表该位置的标志符

例如：http://www.example.com/index.html#print
代表网页index.html的print位置，浏览器读取到这个URL后，会自动滚动到print区域

前端中，可以使用标签的 **name** 或者 **id** 属性跳定义锚点

**#** 是用来指导浏览器动作的，对服务器完全无用，所以，客户端的Request中不包括 **#**

#### （1）#后面的字符

在浏览器中输入URL后，若带有 **#**，则其后面出现的字符，都会被当成锚点，不会被发送到服务端

示例：

```
http://www.example.com/?color=#fff
```

这个URL的本意是传递颜色值，但是因为有 **#**，后面的fff被当成锚点，浏览器并没有发出去

#### （2）改变#不会刷新网页

改变 **#** 只会触发浏览器的滚动，不会重新加载网页

#### （3）改变#会改变浏览器的访问历史

每次改变 **#** 都会在浏览器的浏览历史中新增一条记录，可以使用后退按钮，退回到之前的位置

#### （4）onhashchange事件

H5新增的事件，改变 **#** 会触发这个事件，

### 2、？作用

1. 起连接作用，连接域名和参数；
2. 清除缓存，重新发起请求，因为在做http请求的时候，如果浏览器检测到你的地址完全没变，会从缓存里读取先前请求过的数据，不再发送请求。有些时候是页面资源的加载，有些时候是API的get请求，都有可能。加上 **？** ，会让浏览器认为这是一个新的地址，从而保证重新获取资源；

### 3、&作用

多个参数之间的连接符

# 三、请求消息Request

客户端发送一个HTTP的请求到服务器的消息格式包括：请求行（request line）、请求头部（header）、空行和请求数据四个部分，图示如下：

![img](http_request.png)

本地简单编写一个web服务器，映射request路径/type，返回一个h1包裹的Hello World，使用Fiddler抓包如下：

```http
GET http://192.168.10.105:8069/type HTTP/1.1
Host: 192.168.10.105:8069
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: session_id=25b8c6ba2bbd51287bd813f961ad2dfa8f3a8759

```

注：最后有一个空行，属于第三部分的空行，是必须的

## 示例说明

**（1）请求行：**

```http
GET http://192.168.10.105:8069/type HTTP/1.1
```

- **GET** 说明请求方法为GET方法；
- **http://192.168.10.105:8069/type** 表示请求的URL；
- **HTTP/1.1** 表示使用的是HTTP1.1版本；

**（2）请求头部**

第二行起都是请求头

```http
Host: 192.168.10.105:8069
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/73.0.3683.86 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cookie: session_id=25b8c6ba2bbd51287bd813f961ad2dfa8f3a8759
```

- **HOST** 指出请求的目的地，即域名和端口，这里是IP地址；
- **Connection: keep-alive** 表示该请求使用长链接，HTTP1.1开始，默认使用长链接，若不使用，则HTTP头中会是 **Connection: close**；
- **Cache-Control** 的值可以有两种：**no-cache** 和 **max-age=x**，这里表示想服务器发送http请求确认资源是否有修改；
- **Upgrade-Insecure-Requests: 1** 表示将http升级成https；
- **User-Agent** 代表浏览器的标识；
- **Accept** 告诉服务器，客户端能够接收哪些格式的内容；
- **Accept-Encoding** 是浏览器发给服务器，声明浏览器支持的编码类型；
- **Accept-Language** 告诉服务端，浏览器支持的语言；
- **Cookie** 告诉服务端，请求者的身份信息；

**（3）空行**

请求头后面的空行是必须的

**（4）请求数据**

本例中，请求数据为空

## 请求方法

HTTP1.0定义了三种请求方法：GET、POST和HEAD

HTTP1.1在1.0的基础上新增了五种方法：

| 请求方法 | 说明                                                         |
| -------- | ------------------------------------------------------------ |
| GET      | 请求指定的页面信息，并返回实体主体                           |
| POST     | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改 |
| HEAD     | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| PUT      | 从客户端向服务器传送的数据取代指定的文档的内容               |
| DELETE   | 请求服务器删除指定的资源                                     |
| CONNECT  | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器      |
| OPTIONS  | 允许客户端查看服务器的性能                                   |
| TRACE    | 回显服务器收到的请求，主要用于测试或诊断                     |

## GET和POST区别

参考：https://www.zhihu.com/question/28586791

### 1、携带数据的方式

**GET方法通过URL提交数据，数据再URL中可以看到；POST的数据放在请求数据体中**

> 当浏览器发出一个GET请求时，就意味着要么是用户自己在浏览器的地址栏输入，要不就是点击了html里a标签的href中的url。所以其实并不是GET只能用url，而是浏览器直接发出的GET只能由一个url触发。所以没办法，GET上要在url之外带一些参数就只能依靠url上附带querystring。但是HTTP协议本身并没有这个限制。
>
> 浏览器的POST请求都来自表单提交。每次提交，表单的数据被浏览器用编码到HTTP请求的body里。浏览器在POST一个表单时，url上也可以带参数，只要\<form action="url"\>里的url带querystring就行。只不过表单里面的那些用\<input\> 等标签经过用户操作产生的数据都在会在body里。
>
> 因此我们一般会**泛泛的说**“GET请求没有body，只有url，请求数据放在url的querystring中；POST请求的数据在body中“。但这种情况仅限于浏览器发请求的场景。

### 2、缓存

- GET请求可以被缓存，POST不能被缓存；
- GET能收藏为书签，POST不能，浏览器会提醒是否重复提交；

**GET**

> GET，“读取“一个资源。比如Get到一个html文件。反复读取不应该对访问的数据有副作用。比如”GET一下，用户就下单了，返回订单已受理“，这是不可接受的。没有副作用被称为“幂等“（Idempotent)。
>
> 因为GET因为是读取，就可以对GET请求的数据做缓存。这个缓存可以做到浏览器本身上（彻底避免浏览器发请求），也可以做到代理上（如nginx），或者做到server端（用Etag，至少可以减少带宽消耗）

**POST**

> POST，在页面里\<form\> 标签会定义一个表单。点击其中的submit元素会发出一个POST请求让服务器做一件事。这件事往往是有副作用的，不幂等的。不幂等也就意味着不能随意多次执行。因此也就不能缓存。比如通过POST下一个单，服务器创建了新的订单，然后返回订单成功的界面。这个页面不能被缓存。试想一下，如果POST请求被浏览器缓存了，那么下单请求就可以不向服务器发请求，而直接返回本地缓存的“下单成功界面”，却又没有真的在服务器下单。那是一件多么滑稽的事情。因为POST可能有副作用，所以浏览器实现为不能把POST请求保存为书签。想想，如果点一下书签就下一个单，是不是很恐怖？。此外如果尝试重新执行POST请求，浏览器也会弹一个框提示下这个刷新可能会有副作用，询问要不要继续。
>
> 当然，服务器的开发者完全可以把GET实现为有副作用；把POST实现为没有副作用。只不过这和浏览器的预期不符。**把GET实现为有副作用是个很可怕的事情**。 我依稀记得很久之前百度贴吧有一个因为使用GET请求可以修改管理员的权限而造成的安全漏洞。反过来，把没有副作用的请求用POST实现，浏览器该弹框还是会弹框，对用户体验好处改善不大。
>
> 但是后边可以看到，将HTTP POST作为接口的形式使用时，就没有这种弹框了。于是把一个POST请求实现为幂等就有实际的意义。POST幂等能让很多业务的前后端交互更顺畅，以及避免一些因为前端bug，触控失误等带来的重复提交。将一个有副作用的操作实现为幂等必须得从业务上能定义出怎么就算是“重复”。如提交数据中增加一个dedupKey在一个交易会话中有效，或者利用提交的数据里可以天然当dedupKey的字段。这样万一用户强行重复提交，服务器端可以做一次防护

### 3、安全性

**GET安全性较差，POST比GET安全**

- GET请求的参数是URL的一部分，可以看到明文，POST是放在数据体中的，但是两个其实都不安全；
- GET请求参数会保存在浏览器历史中，POST不会保存；
- GET因为使用URL，所以只允许ASCII字符，POST则无限制；

> 我们常听到GET不如POST安全，因为POST用body传输数据，而GET用url传输，更加容易看到。但是从攻击的角度，无论是GET还是POST都不够安全，因为HTTP本身是**明文协议**。**每个HTTP请求和返回的每个byte都会在网络上明文传播，不管是url，header还是body**。这完全不是一个“是否容易在浏览器地址栏上看到“的问题。
> 为了避免传输中数据被窃取，**必须做从客户端到服务器的端端加密。业界的通行做法就是https**——即用SSL协议协商出的密钥加密明文的http数据。这个加密的协议和HTTP协议本身相互独立。如果是利用HTTP开发公网的站点/App，要保证安全，https是最最基本的要求。
> 当然，端端加密并不一定非得用https。比如国内金融领域都会用私有网络，也有GB的加密协议SM系列。但除了军队，金融等特殊机构之外，似乎并没有必要自己发明一套类似于ssl的协议。
>
> 回到HTTP本身，的确GET请求的参数更倾向于放在url上，因此有更多机会被泄漏。比如携带私密信息的url会展示在地址栏上，还可以分享给第三方，就非常不安全了。此外，从客户端到服务器端，有大量的中间节点，包括网关，代理等。他们的access log通常会输出完整的url，比如nginx的默认access log就是如此。如果url上携带敏感数据，就会被记录下来。但请注意**，就算私密数据在body里，也是可以被记录下来的**，因此如果请求要经过不信任的公网，避免泄密的**唯一手段就是https**。

### 4、编码

GET使用application/x-www-form-urlencoded，POST使用application/x-www-form-urlencoded 或 multipart/form-data。为二进制数据使用多重编码。

> 常见的说法有，比如GET的参数只能支持ASCII，而POST能支持任意binary，包括中文。但其实从上面可以看到，GET和POST实际上都能用url和body。因此所谓编码确切地说应该是http中url用什么编码，body用什么编码。

### 5、数据长度的限制

因为GET使用URL添加数据，URL最大的长度是2048，POST则无限制

> 因为上面提到了不论是GET和POST都可以使用URL传递数据，所以我们常说的“GET数据有长度限制“其实是指”URL的长度限制“。
>
> HTTP协议本身对URL长度并没有做任何规定。实际的限制是由客户端/浏览器以及服务器端决定的。
>
> 先说浏览器。不同浏览器不太一样。比如我们常说的2048个字符的限制，其实是IE8的限制。并且原始文档的说的其实是“URL的最大长度是2083个字符，path的部分最长是2048个字符“。
>
> IE8之后的IE URL限制我没有查到明确的文档，但有些资料称IE 11的地址栏只能输入法2047个字符，但是允许用户点击html里的超长URL。
>
> Safari，Firefox等浏览器也有自己的限制，但都比IE大的多。然而新的IE已经开始使用Chrome的内核了，也就意味着“浏览器端URL的长度限制为2048字符”这种说法会慢慢成为历史。

服务端限制长度：

> 除了浏览器，服务器这边也有限制，比如apache的LimieRequestLine指令。
>
> 为啥要限制呢？如果写过解析一段字符串的代码就能明白，解析的时候要分配内存。对于一个字节流的解析，必须分配buffer来保存所有要存储的数据。而URL这种东西必须当作一个整体看待，无法一块一块处理，于是就处理一个请求时必须分配一整块足够大的内存。如果URL太长，而并发又很高，就容易挤爆服务器的内存；同时，超长URL的好处并不多，我也只有处理老系统的URL时因为不敢碰原来的逻辑，又得追加更多数据，才会使用超长URL。
> 对于开发者来说，使用超长的URL完全是给自己埋坑，需要同时要考虑前后端，以及中间代理每一个环节的配置。此外，超长URL会影响搜索引擎的爬虫，有些爬虫甚至无法处理超过2000个字节的URL。这也就意味着这些URL无法被搜到

# 四、请求响应Response

HTTP的响应由四个部分组成，分别是：状态行、消息包头、空行和响应正文

上例中的响应内容如下：

```http
HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 20
Set-Cookie: session_id=25b8c6ba2bbd51287bd813f961ad2dfa8f3a8759; Path=/
Server: Werkzeug/0.11.11 Python/2.7.11
Date: Sun, 19 Jan 2020 06:33:02 GMT

<h1>Hello World</h1>
```

## 示例说明

**（1）状态行**

```http
HTTP/1.0 200 OK
```

- **HTTP/1.0** 表示协议版本；
- 200是状态码，表示连接成功；

**（2）消息报头**

```http
Content-Type: text/html; charset=utf-8
Content-Length: 20
Set-Cookie: session_id=25b8c6ba2bbd51287bd813f961ad2dfa8f3a8759; Path=/
Server: Werkzeug/0.11.11 Python/2.7.11
Date: Sun, 19 Jan 2020 06:33:02 GMT
```

- **Content-Type: text/html; charset=utf-8** 表示返回的页面内容是html，编码是utf-8；
- **Content-Length: 20** 表示响应正文的长度为20；
- **Set-Cookie** 表示响应头将cookie信息发给浏览器；
- **Server** 表示服务器的一些信息；
- **Date** 表示响应时间，GMT是格林尼标准时间，这里需要+8才是北京时间；

**（3）空行**

消息报头后面的空行是必须的

**（4）响应正文**

```html
<h1>Hello World</h1>
```

## Response状态码

状态码有五种

| 状态码类别 | 说明                                     |
| ---------- | ---------------------------------------- |
| 1xx        | 信息提示，表示请求已被成功接收，继续处理 |
| 2xx        | 请求被成功提交                           |
| 3xx        | 客户端被重定向到其他资源                 |
| 4xx        | 客户端错误状态码，格式错误或者不存在资源 |
| 5xx        | 描述服务器内部错误                       |

常见的状态码有：

| 常用状态码 | 说明                                     |
| ---------- | ---------------------------------------- |
| 200        | 客户端请求成功                           |
| 302        | 重定向                                   |
| 404        | 请求资源不存在                           |
| 400        | 客户端请求有语法错误，不能被服务器所理解 |
| 401        | 请求未经授权                             |
| 403        | 服务器收到请求，但是拒绝提供服务         |
| 500        | 服务器内部错误                           |
| 503        | 服务器当前不能处理客户端的请求           |

# 五、持久连接

HTTP协议使用“请求-应答”模式，即“非Keep-Alive”模式。而当使用“Kepp-Alive”时，就可以保证客户端和服务端的连接持久有效，当出现需要对服务器的后继请求时，能避免重新建立连接。

## 1、如何使用持久连接

HTTP1.0版本中，并没有官方的标准来规定Keep-Alive，所以，Keep-Alive是被附加到HTTP1.0版本的协议中的，即，如果客户端浏览器支持Keep-Alive，那么就在HTTP的请求头中，添加一个字段 **Connection: Keep-Alive**，当服务器收到带有此字段的请求后，就不会断开连接，当客户端再次发送请求时，就是用该条已建立的连接。

而在HTTP1.1的版本中，默认都保持连接，需要在请求中加入 **Connection: close** 告诉服务端不使用Keep-Alive，那么服务端才会在一条请求结束后，关闭连接。

## 2、持久连接的字段

如1中所说，使用字段 **Connection: Keep-Alive** 即可使用长连接，但是长连接不能一直保持，可以使用字段的参数设置长连接的参数

```http
Keep-Alive: timeout=5, max=100
```

**timeout=5** 表示长连接保持5秒，**max=100** 表示此长连接最多接受100此请求就断开