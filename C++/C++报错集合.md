## undefined reference to `htonl@4'

windows下使用头文件 `#include <winsock.h>`出现

解决方法：

链接 ***wsock32*** 库

pro文件中，添加 ***LIBS += -lws2_32***

VS中，project->properties->(左边)c/c++Build->(中间)GCC C Linker里边的Libraries->(右边)上边的写上wsock32

