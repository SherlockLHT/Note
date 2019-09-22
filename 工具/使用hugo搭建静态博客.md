[TOC]

# 一、说明

本文将会介绍使用Hugo搭建静态博客的方法，主要内容来源是官方文档，Hugo是使用Go语言实现的静态网站生成器，优点是简单易用。

官方文档：https://www.gohugo.org

# 二、Hugo的使用

## 1、安装Hugo

在官网下载二进制安装文件（hugo或者hugo.exe），windows下因为需要使用hugo.exe指令，建议将其添加到环境变量中，以避免需要目录的来回切换

## 2、生成站点

使用指令生成站点

```
./hugo.exe new site 路径
```

如此，在指定的路径下，就会生成Hugo的初始目录，结构如下：

```
▸ archetypes/
▸ content/
▸ layouts/
▸ static/
  config.toml
```

## 3、创建一篇文章

```
./hugo.exe new first.md
```

新生成的 **first.md**将会出现在 **content** 目录下，打开会发现，里面已经有一些内容了，这些内容可以修改

```
---
title: "第一篇文章"
date: 2019-09-20T22:09:06+08:00
draft: true
---
```

在下面添加文章的正文内容即可

- **draft** 表示这是一个草稿，为true，Hugo将会跳过该文章，除非指定不跳过；
- **content** 目录就是用作存放文章的目录

## 4、安装皮肤

可以去官方的皮肤列表中挑选一个皮肤，本文以官网的 **vienna** 主题为例

先将主题的源码下载到本地的 **theme** 目录下（该目录用于保存主题）

```
cd theme
git clone 主题url
```

## 5、在本地运行Hugo

目录切换至根目录下，执行指令运行

```
hugo server --theme=vienna --buildDrafts
```

- **--theme=vienna** 表示使用主题**vienna**；
- **--buildDrafts** 表示连带草稿也一并生成静态页面；

在浏览器中打开url：**http://localhost:1313** 即可看到效果，并且，v0.15版本之后，默认修改本地文件，会自动刷新，不需要手动重新生成

## 6、部署

官网的说明介绍了部署在GitHub Pages上，但是国内访问github的速度是在太慢，所以，退而求其次，本次搭建在gitee上。

具体的部署说明需要参见托管网站的使用说明。

在gitee上新建仓库，使用gitee的用户名作为仓库名（此作用是url避免使用二级路径），在站点根目录下执行指令

```
hugo.exe --theme=vienna --baseUrl="https://user_name.gitee.io/"
```

- 此指令的作用是，将本地 **content** 目录下的文章生成静态页面；
- 此指令不包括草稿，注意将需要生成的文章的开头的`draft=true` 删掉；
- 注意这里的url最后有一个 **/**，如果丢失，将会404；

执行完毕之后，所有的静态页面都会生成到 **public** 目录下，将此目录下的所有内容push到仓库中

push完毕后，点击仓库上方的 **Services** - **Gitee Pages**，切换到gitee的pages的生成页面，选择分支，点击生成，等待完成即可

第一次是开始生成，以后有更改，点击进去后，需要更新即可

完成后，访问 **https://user_name.gitee.io** 即可看到页面

可以参考我的博客：https://sherlock_lin.gitee.io，因为初搭，还有很多不完善的地方，以后会抽时间，自己做一个主题

