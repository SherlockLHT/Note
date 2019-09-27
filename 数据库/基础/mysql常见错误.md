### MySQL服务正在启动或停止中，请稍候片刻后再试一次

> 参考：https://cloud.tencent.com/developer/article/1380468

原因一般是mysql的有残留进程没有结束

解决方法：

使用管理员权限打开cmd，输入指令***tasklist| findstr "mysql"*** 查找mysql的残留进程，如果有，则使用指令 ***taskkill/f /t /im mysqld.exe*** 将残余进程杀死，然后在看看有没有残留进程，知道全部杀死为止，最后启动mysql

