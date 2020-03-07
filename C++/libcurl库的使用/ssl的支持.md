# 一、场景

在 windows10 平台下，编译好 curl 并开始使用后发现，请求的 url 如果是 http 协议的，执行正常，如果是 https 协议的，就会报错，错误描述为 **Unsupported Protocol**，经查询，该错误可能和SSL有关。

网上搜到的相关的技术博客大多是复制粘贴，排版乱七八糟的不说，内容也是一塌糊涂，但是也总有一些有价值的，比如：https://blog.csdn.net/u012814856/article/details/81876508

# 二、问题原因查询

能确定大概方向，我们就可以朝着这个方向去查询。

打开 doc 目录下的 INSTALL.md 文件查看使用方法。在Windows-MingW32 一节下，我们可以看到，libcurl 的构建需要通过额外的指令，以达到支持某些库的目的，比如SSL、Zlib等，本次的排查只针对 SSL，其他的功能情况估计类似。

根据文档说明，在根目录下，执行指令 **make mingw32-ssl-zlib**，但是构建失败，提示文字是：

```makefile
make -C lib -f Makefile.m32 CFG=mingw32-ssl-zlib
make[1]: Entering directory 'F:/Code/libcurl/curl-7.68.0/lib'
Makefile.m32:257: *** Invalid path to OpenSSL package: ../../openssl-1.0.2a.  Stop.
make[1]: Leaving directory 'F:/Code/libcurl/curl-7.68.0/lib'
makefile:61: recipe for target 'mingw32-ssl-zlib' failed
make: *** [mingw32-ssl-zlib] Error 2
```

由错误提示不难看出，报错的地方在 **Makefile.m32** 文件的第257行，原因根据描述，大概是OpenSSL包的地址无效，后面接着打印出一个相对路径，估计就是OpenSSL包的路径吧。

打开 **Makefile.m32** 文件，直接看第257行，如下：

```makefile
$(error Invalid path to OpenSSL package: $(OPENSSL_PATH))
```

这里的 **OPENSSL_PATH** 是一个变量，找到这个变量的设置语句，在46~49行：

```makefile
# Edit the path below to point to the base of your OpenSSL package.
ifndef OPENSSL_PATH
OPENSSL_PATH = ../../openssl-1.0.2a
endif
```

注释说，编辑下面的变量，要指向OpenSSL包，就是说，**OPENSSL_PATH** 这个变量是 OpenSSL包的路径。

到现在，问题的原因能够确定了，libcurl 库并不是一个独立的库，它的一些功能需要依赖一些其他的库，而默认编译的时候不会带入这些的。若我们需要使用相对应的功能，就需要自己加入需要的库，并在构建 libcurl 的时候，使能某些功能。

# 三、解决问题

既然问题原因找到了，那么如何解决呢？两种方法：

**法一：**

既然缺了 OpenSSL 库，那就下载添加不就完了，如何下载构建 OpenSSL 库，这里不提，在 **Makefile.m32** 文件中设置好包路径，再次使用 **make mingw32-ssl-zlib** 指令编译即可。

**法二：**

法一需要构建 OpenSSL 并重新编译，耗时耗力，如果我们能下载到 libcurl 对应的库文件，是不是可以避免自己花费大量的时间来构建呢？答案是可以的，且官方也提供了下载路径。

在官网的下载页面，我们找到对对应的平台，可以看到有好几个链接，后面标记 **binary** 表示这是一个二进制文件，即编译好的，我们只需要进入，下载需要的版本即可，里面包含了需要的库和dll文件，添加到项目中即可。

# 四、linux

以上描述都是在windows系统上，linux下其实也是类似的，想ubuntu等系统尚且能下载到编译好的库文件，而想centos等系统只能用源码方式安装，，那么openssl 的安装比不可少，这里简单介绍一下linux下的安装步骤

linux 下带有 SSL 的源码的构建的指令如下：

```
./configure --with-ssl
make
make install
```

一般情况下，linux 是不会有ssl 库的，需要手动安装，所以 ./configure --with-ssl 会失败，提示找不到 OpenSSL 库，安装即可

本次以 openssl-1.1.1d 为例，解压后使用以下指令构建即可

```
./config 或者 ./Configure
make
make install
```

OpenSSL 安装完成之后，再次安装 libcurl 即可，具体问题可以参考源码目录的 **doc/INSTALL.md** 中的说明

libcurl 既然使用SSL功能，则库添加到项目中后，对应的项目也需要添加 SSL 库支持，使用 OpenSSL 即可，CMakeLists.txt 需要修改的内容如下：

```cmake
target_link_libraries(${PROJECT_NAME} 
	${PROJECT_SOURCE_DIR}/libs/libcurl.a
	ssl
	crypto
	pthread
)
```

除了 libcurl 外，SSL的支持需要 libssl.a 和 libcrypto.a 库



