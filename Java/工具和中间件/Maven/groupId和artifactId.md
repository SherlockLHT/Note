https://www.cnblogs.com/zhaiyf/p/9077402.html#commentform

### maven中的groupId和artifactId到底指的是什么?

**groupid** 和 **artifactId** 被统称为“坐标”是为了保证项目唯一性而提出的，如果你要把你项目弄到maven本地仓库去，你想要找到你的项目就必须根据这两个id去查找。

**groupId** 一般分为多个段，这里我只说两段，第一段为域，第二段为公司名称。

域又分为 **org、com、cn** 等等许多，其中 **org** 为非营利 组织，**com** 为商业组织A。举个apache公司的tomcat项目例子：这个项目的groupId是org.apache，它的域是org（因为tomcat是非营利项目），公司名称是apache，artigactId是tomcat。

比如我创建一个项目，我一般会将 **groupId** 设置为 **cn.sherlock**，cn表示域为中国，sherlock是我个人姓名缩写，artifactId设置为testProj，表示你这个项目的名称是testProj，依照这个设置，你的包结构最好是cn.sherlock打头的，如果有个StudentDao，它的全路径就是cn.sherlock.testProj.dao.StudentDao

可以这样理解，**groupId** 是这个项目的开发组织的唯一标识符，实际对应Java的包结构，是main目录里面java的目录结构。**artifactId** 是项目的唯一标识符，实际对应项目的名称，是项目的根目录的名称。