[TOC]

- 目前最先进的分布式版本管控系统

## 一、初始化配置

```
git config --global user.name "sherlock"
git config --global user.email mail@mail.com
```

## 二、常用指令

#### 1、基本指令

```python
git --help	：帮助
cd			：打开路径
cd ..		：打开上一级路径
pwd			：显示当前路径
mkdir		：新建文件夹
git init	：在当前目录下初始化本地仓库
```

#### 2、添加文件

```python
git add 123.txt		：将123.txt文件添加到暂存区
git add .			：将本地所有文件添加到暂存区
git commit -m "注释" ：将暂存区的所有文件提交到仓库
git status			：常看当前暂存区状态
git diff 123.txt	：查询文件修改内容
git commit --amend	：修改上一次提交的commit
```

#### 3、忽略文件

```python
touch .gitignore	：创建.gitignore文件
```

##### 文件内容说明：

```python
/new/		：过滤new文件夹下所有内容
*.rar		：过滤所有后缀名为.rar的文件
111.txt		：过滤指定文件
/new/111.txt：过滤指定目录下的指定文件
```

#### 4、版本回退和撤销

```python
git log					：查询log，日志太长按q退出
git log -2				：查询log，显示2个commit
git log --stat --summary：查询log，显示每次版本的详细变化
git log --pretty=online	：查询log，显示所有更改信息（简化）
git reflog				：查询版本号
git reset -- 123.txt	：撤回最近一次add的123.txt文件，在未commit之前
git reset --hard head^	：往上回退一个版本
git reset --hard head^^	：往上回退两个版本
git reset --hard head~3	：往上回退三个版本
git reset --hard version：回退至指定版本
git checkout -- 123.txt	：撤销123.txt文件保存在工作区的修改，在未add之前，同svn revert
```

#### 5、其他

```python
cat 123.txt		：查看文件内容
rm 123.txt		：删除123.txt，在未commit之前可以使用checkout指令恢复
gitk			：内建的图形化git
git config color.ui true	：彩色的git输出
git config format.pretty oneline	：显示历史记录，只显示一行注释信息
git add -i		：交互地添加文件至缓存区
```

#### 6、创建分支

- 分支用来分离某些特定的开发；
- init时，默认主分支是master；
- 可以在其他分支上进行开发，完成之后再将其合并到主分支上；

##### （1）基本分支指令

```python
git branch			：查看所有分支
git branch test		：在当前分支基础上新建名为test的分支
git checkout test	：切换到test分支
git branch -d test	：删除test分支
git checkout -b test：新建并切换到test分支
```

##### （2）合并分支

```python
git merge test	：将test分支合并到当前分支
```

- 自定合并有时候会出现conflicts，需要手动修改解决，然后add/commit

#### 7、远程仓库

```python
git remote add origin xxxx	：连接远程仓库
git remote rm origin		：断开远程仓库
git push -u origin master	：推送到远程origin仓库的maste分支，同时指定origin为默认仓库
git push origin master		：推送到远程origin仓库的master分支
git push					：推送到默认仓库的默认分支，需指定默认仓库
git diff test origin/aa		：比较本地test仓库和远程origin仓库的aa分支的不同
git diff origin/aa			：比较当前本地仓库和远程origin仓库的aa分支的不同
```

```python
git remote		：查看远程仓库
git remote -v	：产看远程仓库的实际链接地址
git clone xxx	：从指定地址copy内容到本地
git fetch		：从连接的远程仓库下载最新的分支和数据
git fetch origin：从连接的远程仓库下载仓库名为origin的数据
git merge		：将从远程仓库下载的数据合并到当前的分之上
```

##### 忽略本地修改，更新远程修改到本地
```python
git fetch --all
git reset --hard origin/master
```

## 三、常见错误及解决方式

##### （1）执行git add file_name时出现如下错误：

```python
If no other git process is currently running, this probably means a git process crashed in this repository earlier. Make sure no other git process is running and remove the file manually to continue.
```

出现原因：未知

解决方法：

```python
rm -f ./.git/index.lock
```

##### （2）编译ICS时，出现如下错误：

```python
build/core/java.mk:20: *** dalvik/dexgen: Invalid LOCAL_SDK_VERSION '4' Choices are: current .  Stop.
```

出现原因：未知

解决方法：

```python
rm -rf prebuilt;repo sync prebuilt
```

##### （3）错误提示
```python
A lock file already exists in the repository, which blocks this operation from completing
```
出现原因：上一次提交完成之前被打断
解决方法：删除/.git/index/lock
```python
rm -f .git/index.lock   //linux
del .git\index.lock     //window
```