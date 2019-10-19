

## 使用Qt SQL库

头文件：

```c
#include <QtSql>
```

项目：

```js
QT += sql
```

## 常用类

**QSqlDatabase：**数据库的连接打开等操作

**QSqlQuery：**执行语句，获取结果

**QSqlRecord：**封装了数据库的所有记录

## 示例

```c++
//这里的123和456是为了区分两个数据库的连接
QSqlDatabase database_1 = QSqlDatabase::addDatabase("QMYSQL", "111");
database_1.setHostName("127.0.0.1");//主机地址
database_1.setDatabaseName("test");	//数据库名称
database_1.setUserName("root");		//登入数据库使用的用户名
database_1.setPassword("ajdts");	//密码

QSqlDatabase database_2 = QSqlDatabase::addDatabase("QMYSQL", "222");
database_2.setHostName("127.0.0.1");
database_2.setDatabaseName("test_2");
database_2.setUserName("root");
database_2.setPassword("ajdts");

database_1.open()//打开数据库
database_2.open()
    
//QSqlQuery query(database_1);
QSqlQuery query(database_2);
QSqlQuery query(QSqlDatabase::database("222"));
QString sql_statment = QString("select name from table_1 where id=1;");
if(!query.exec(sql_statment))
{
	qDebug()<<"exec fail"<<query.lastError().text();
	return;
}
qDebug()<<"Size:"<<query.size();
while(query.next())
{
	int index = query.record().indexOf("name");
	qDebug()<<query.value(index).toString();
}
//插入数据
sql_statment = QString("insert into table_1 (`name`, `price`) value (?, ?);");
query.prepare(sql_statment)

query.addBindValue("abc");
query.addBindValue(123.45);
query.exec()

database_1.close();	//关闭数据库
database_2.close();
```

### 数据库类型和连接名

```c++
QSqlDatabase database_1 = QSqlDatabase::addDatabase("QMYSQL", "111");
```

```c++
//根据连接名获取数据库
QSqlDatabase database_1 = QSqlDatabase::database("123");
```

- **QMYSQL** 表示使用 **mysql**，Qt支持的数据可以参开开发手册；
- 可以同时连接并打开多个数据库，在新增数据库时传入连接名，可以区分，若不传入，Qt使用默认连接名；
- 后面可以使用连接名获取数据库；

### 执行语句和获取结果

```c++
QSqlQuery query(database_2);
query.exec(sql_statment);
```
- 根据数据库获取query对象；
- 使用 **exec()** 方法执行语句

```c++
query.next();//获取结果集前需要使用 **next()** 方法；
```
```c++
query.size();//结果的数量
```

```c++
int index = query.record().indexOf("name");	//获取名称在结果中的下标
qDebug()<<query.value(index).toString();	//根据下表获取结果
qDebug()<<query.value("name").toString();	//根据名称获取结果
```

### 插入数据

```c++
sql_statment = QString("insert into table_1 (`name`, `price`) value (?, ?);");
query.prepare(sql_statment)

query.addBindValue("abc");
query.addBindValue(123.45);
query.exec()
```

- 插入数据可以直接拼接sql语句，也可以使用上面占位符的方法；

## 常见错误

```c
QSqlDatabase: QMYSQL driver not loaded
QSqlDatabase: available drivers: QSQLITE QMYSQL QMYSQL3 QODBC QODBC3 QPSQL QPSQL7
```

**原因：**

​	缺少数据库驱动，若是使用 **sqlite** 则不需要额外驱动，qt自带；若是其他数据库，还需要安装对应的数据库。

​	如 **mysql**，除了qt的 **mysql** 驱动外，还需要安装 **mysql** 

**解决方法：**

1. 将Qt的数据库驱动复制到运行路径下（一般位于qt安装目录的 **sqldrivers** 文件夹下）；
2. 安装 **mysql** ，并将链接库库复制了运行路径下（**libmysql.dll**）