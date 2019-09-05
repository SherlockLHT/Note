[TOC]

## 1、查看端口占用程序

```
netstat -ap | grep 8080     #查看8080端口
lsof -i:8080                #查看8080端口的进程占用
```

若需要接触端口占用，使用kill + pid关闭进程即可

## 2、查看文件

```python
ll		#同ls -l
ls		#查看当前目录下的文件
ls -l	#查看文件的详细信息
```

## 3、在xshell中上传和下载文件

> 参考自：https://www.cnblogs.com/qq240115928/p/7112482.html

以下目录可以在xshell工具下使用，此指令均针对linux

```pyhton
sz filename		#send，即发送文件，会弹框选择需要保存的路径
rz 				#receive，即接收文件，会弹框选择需要上传的文件
```

### 若报错：-bash: rz: command not found

表示没有安装lrzsz，不能执行该命令，安装即可，cmd如下：

```
# yum -y install lrzsz
```

## 4、删除

用于删除文件和文件夹

```
rm -rf #向下递归强行直接删除
 -r表示向下递归
 -f表示强行删除，
```

