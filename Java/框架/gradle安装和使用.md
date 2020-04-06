# 1、说明

gradle 和 Maven 一样，是一款自动化构建工具，因为项目需要，这里记录一下安装和一些配置方法

# 2、安装和配置

首先，gradle 依赖 Java，首先把 Java 环境配置好，本次使用 JDK8

前往官网下载源码，解压到硬盘上即可，剩下的就是一些环境的配置

## 2.1、系统环境

新增系统环境变量 **GRADLKE_HOME**，值为 gradle 的安装路径，如：E:\Program Files\gradle-6.3

在系统变量PATH中新增 gradle 目录下 bin 目录的路径

配置完成之后，使用**gradle -v** 指令查看

```cmd
λ gradle -v

Welcome to Gradle 6.3!

Here are the highlights of this release:
 - Java 14 support
 - Improved error messages for unexpected failures

For more details see https://docs.gradle.org/6.3/release-notes.html


------------------------------------------------------------
Gradle 6.3
------------------------------------------------------------

Build time:   2020-03-24 19:52:07 UTC
Revision:     bacd40b727b0130eeac8855ae3f9fd9a0b207c60

Kotlin:       1.3.70
Groovy:       2.5.10
Ant:          Apache Ant(TM) version 1.10.7 compiled on September 1 2019
JVM:          1.8.0_121 (Oracle Corporation 25.121-b13)
OS:           Windows 10 10.0 amd64
```

如上，表示安装成功

## 2.2、本地仓库路径

gradle 默认的本地仓库在C盘，但也可以自定义目录，修改方式有如下几种，建议使用法一：

**法一：**

直接添加环境变量即可，此法对 linux 系统也适用

```
GRADLE_USER_HOME=E:\Program Files\gradle-6.3\.gradle
```

**法二：**

使用指令行设置，使用 -g 参数

```cmd
gradle -g E:\Program Files\gradle-6.3\.gradle
```

**法三：**

使用IDE中的 gradle 设置

## 2.3、包下载镜像

和 maven 一样，官方的包路径导致下载依赖缓慢，国内可以使用阿里云镜像，配置方法有两种

**法一：针对项目配置**

该修改只对当前项目生效

编辑项目目录下的 build.gradle 如下：

```properties
repositories {
	maven{ url 'http://maven.aliyun.com/nexus/content/groups/public/'}
	mavenCentral()
}
```

**法二：全局配置方式**

找到 gradle 安装目录下的 .gradle 目录下的 init.gradle 文件（没有就创建），编辑如下：

```properties
allprojects{
    repositories {
        def ALIYUN_REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public'
        def ALIYUN_JCENTER_URL = 'http://maven.aliyun.com/nexus/content/repositories/jcenter'
        all { ArtifactRepository repo ->
            if(repo instanceof MavenArtifactRepository){
                def url = repo.url.toString()
                if (url.startsWith('https://repo1.maven.org/maven2')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_REPOSITORY_URL."
                    remove repo
                }
                if (url.startsWith('https://jcenter.bintray.com/')) {
                    project.logger.lifecycle "Repository ${repo.url} replaced by $ALIYUN_JCENTER_URL."
                    remove repo
                }
            }
        }
        maven {
            url ALIYUN_REPOSITORY_URL
            url ALIYUN_JCENTER_URL
        }
    }
}
```



