[TOC]

## 作用

1. 封装数据库接口，避免拼接冗长的 ***sql*** 语句；
2. 将数据库的属性和列相互对应映射，方便操作；

## 代码示例

### 需求：

1. 有 ***Product***、***Category*** 和 ***User*** 三种数据，实现对三种数据的数据库增删查改操作；
2. ***Product*** 有 ***id*** 、 ***name*** 和 ***price*** 属性；
3. ***Category*** 有 ***id***、***name*** 属性；
4. ***User*** 有 ***id***、***name*** 属性；
5. 一个 ***Product*** 只能属于一个 ***Category***，多个 ***Product*** 可以属于同一个 ***Category***；
6. 一个 ***User*** 可以购买多个 ***Product***，多个 ***User*** 可以购买同一个 ***Product***；

### 需求分析：

1. 三种数据至少建三个 ***table***，分别是 ***product\_*** 、***category\_*** 和 ***user\_***，三个 ***table*** 每个都有唯一的 ***id***；

2. ***Product*** 和 ***Category*** 是一对多的关系，***Category*** 和 ***Product*** 是多对一的关系，所以 ***Product*** 需要新建列 ***cid***  保存 ***Category*** 的 ***id***；

3. ***Product*** 和 ***User*** 之间是多对多的关系，所以不能新建列保存 ***id***，而应该再新建一个 ***user_product***，保存 ***Product*** 和 ***User*** 映射关系，则 ***user_product*** 有两列：***pid*** 和 ***uid***；

   根据分析，设计出最终的数据库 ***table*** 关系如下所示：

![img](E:/YoudaoNote/sherlock_lin@163.com/9afca28723d1416886cc42566b739a08/b4e99a0991cf4ea28bbdb6e65fd8618c.jpg)

### 代码结构：

1. ***Product.java***，***Product*** 实体类，有 ***id***、***name*** 、***category*** 和 ***users(Set)*** 四个属性；
2. ***Product.hbm.xml***，和 ***Product.java*** 在同级目录下，作为 ***Product*** 属性和数据库的映射关系配置；
3. ***Category.java***，***Category*** 实体类，有 ***id***、***name*** 和 ***products(Set)*** 三个属性
4. ***Category.hbm.xml***，和 ***Category.java*** 在同级目录下，配置 ***Category*** 属性和数据库映射关系；
5. ***User.java***，***User*** 实体类，有 ***id***、***name*** 和 ***products(Set)*** 三个属性；
6. ***User.hbm.xml***，和 ***User.java*** 在同级目录下，配置 ***User*** 属性和数据库映射关系；
7. ***hibernate.cfg.xml***，***src*** 目录下，配置数据库；
8. ***TestHibernate.java***，逻辑测试代码。

### 代码：

#### （1）Product.java

```java
public class Product {
    int id;
    String name;
    float price;
    Category category;
    Set<User> users;
    //getter/setter略
}
```

#### （2）Product.hbm.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">

<hibernate-mapping package="com.how2java.pojo">
    <class name="Product" table="product_">
        <id name="id" column="id">
            <generator class="native" />
        </id>
        <property name="name" />
        <property name="price" />
        <many-to-one name="category" class="Category" column="cid"/>
        <set name="users" table="user_product" lazy="false">
            <key column="pid" />
            <many-to-many column="uid" class="User" />
        </set> 
    </class>
</hibernate-mapping>
```

##### Product.hbm.xml详解：

```xml
<hibernate-mapping package="com.how2java.pojo">
    ...
</hibernate-mapping>
```

​	每一个 ***xxx.hbm.xml*** 都有且只有一个的根元素，包含一些可选的属性，***package*** 指定一个包前缀。

```xml
<class name="Product" table="product_">
    ...
</hibernate-mapping>
```

​	标识一个类的 ***XML*** 应用，***name*** 为类名，***table*** 为表名。

```xml
<id name="id" column="id">
    <generator class="native" />
</id>
```

​	设置主键，***generator*** 设置主键生成策略，***native*** 意味着 ***id*** 的自增长方式采用数据库的本地方式，便于数据库移植，***oracle*** 可以指定 ***sequnce***。

```xml
<property name="name" />
<property name="price" type="string" column="price"/>
```

​	配置属性，***name*** 表明属性名，***type*** 表明类型，***column*** 表明数据库对应列，若不指定***column***，则列使用属性名。

```xml
<many-to-one name="category" class="Category" column="cid"/>
```

- 设置多对一的关系；
- ***name*** 表明是 ***Product*** 中的 ***category*** 属性；
- ***class*** 表明其对应的类；
- ***column*** 表明指向 ***product\_*** 表的 ***cid*** 列是指向***category\_*** 的外键；

有了多对一的关系配置，则可以通过 ***Product*** 对象实例增删查改 ***Category*** 实例。

```xml
<set name="users" table="user_product" lazy="false">
    <key column="pid" />
    <many-to-many column="uid" class="User" />
</set>
```

​	***\<set\>\</set\>***  可以设置一对多和多对多的关系

- 设置多对多的关系，***name*** 表明 ***Product*** 中的 ***users*** 属性；
- ***table*** 表明对多对关系映射到数据库的表；
- ***lazy*** 表明不使用延迟加载；
- ***key*** 表明 ***Product*** 在 ***user_product*** 中的列；
- ***many-to-many*** 表明 ***Product*** 类中指定的属性在 ***product\_*** 中的列，以及对应的类；

有了这个多对多的关系映射，就可以通过 ***Product*** 获取/删改有映射关系的 ***User***。

#### （3）Category.java

```java
public class Category {
    int id = 0;
    String name;
    Set<Product> products;
	//getter/setter略
}
```

#### （4）Category.hbm.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.how2java.pojo">
    <class name="Category" table="category_">
        <id name="id" column="id">
            <generator class="native" />
        </id>
        <property name="name" />
        <set name="products" lazy="false">
            <key column="cid" not-null="false" />
            <one-to-many class="Product" />
        </set>
    </class>
</hibernate-mapping>
```

##### Category.hbm.xml详解：

```xml
<set name="products" lazy="false">
    <key column="cid" not-null="false" />
    <one-to-many class="Product" />
</set>
```

- 设置一对多关系；
- ***name*** 表示设置 ***Category*** 类中的属性；
- ***key*** 表明本类在在一对多的关系中出于对方 ***table*** 的哪一列，即外键；
- ***no-null*** 表明外键可以为空；
- ***one-to-many*** 表明一对多关系对应的 ***Product*** 类；

有了此映射关系，就可以通过 ***Category*** 获取/删改映射的 ***Product***。

#### （5）User.java

```java
public class User {
    int id;
    String name;
    Set<Product> products;
 	//getter/setter略   
}
```

#### （6）User.hbm.xml

```xml
<?xml version="1.0"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://www.hibernate.org/dtd/hibernate-mapping-3.0.dtd">
<hibernate-mapping package="com.how2java.pojo">
    <class name="User" table="user_">
        <id name="id" column="id">
            <generator class="native">
            </generator>
        </id>
        <property name="name" />
        <set name="products" table="user_product" lazy="false">
            <key column="uid" />
            <many-to-many column="pid" class="Product" />
        </set> 	
    </class>
</hibernate-mapping>
```

##### User.hbm.xml详解：

```xml
<set name="products" table="user_product" lazy="false">
    <key column="uid" />
    <many-to-many column="pid" class="Product" />
</set> 
```

- ***set*** 表明设置多对多/一对多关系；
- ***name*** 表明设置的 ***products*** 属性；
- ***table*** 表明关系映射与 ***table*** 中；
- ***key*** 表明本类出于映射表中的列；
- ***many-to-many*** 表明是多对多关系，映射 ***Product*** 类，处于映射表中的 ***pid*** 列；

有了此映射关系，就可以通过 ***User*** 获取/删改映射的 ***Product***。

#### （7）hibernate.cfg.xml

```xml
<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
       "-//Hibernate/Hibernate Configuration DTD 3.0//EN"
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">
<hibernate-configuration>
    <session-factory>
        <!-- Database connection settings -->
        <!-- 指定链接数据库所用的驱动 -->
        <property name="connection.driver_class">
            com.mysql.jdbc.Driver
        </property>
        <property name="connection.url">
            jdbc:mysql://localhost:3306/test?characterEncoding=UTF-8
        </property>
        <property name="connection.username">root</property>
        <property name="connection.password"></property>
        <!-- SQL dialect -->
        <property name="dialect">org.hibernate.dialect.MySQLDialect</property>
        <!-- 这是Hibernate事务管理方式，即每个线程一个事务 -->
        <property name="current_session_context_class">thread</property>
        <!-- 这表示是否在console显示执行的sql语句 -->
        <property name="show_sql">true</property>
        <!-- 这表示是否自动更新数据库的表结构，update表示不需要手动创建表，Hibernate会自动去创建/更新表结构 -->
        <property name="hbm2ddl.auto">update</property>
        <!-- 这表示Hibernate会去识别这个几个实体类 -->
        <mapping resource="com/how2java/pojo/Product.hbm.xml" />
        <mapping resource="com/how2java/pojo/Category.hbm.xml"/>
        <mapping resource="com/how2java/pojo/User.hbm.xml" />
    </session-factory>
</hibernate-configuration>
```

##### hibernate.cfg.xml详解：

```xml
<property name="connection.url">
    jdbc:mysql://localhost:3306/test?characterEncoding=UTF-8
</property>
```

- 指定数据库的 ***url***，即 ***hibernate*** 链接的数据库名；
- 这里的数据库名是 ***test***；
- ***characterEncoding=UTF-8*** 表示指定编码方式；

```xml
<property name="dialect">org.hibernate.dialect.MySQLDialect</property>
```

​	这句表示使用 ***MySQL*** 方言。​何为方言？

​	因为在代码层，开发人员不用关心底层到底使用 ***MySql*** 还是 ***Oracle***，写的代码都是一样的，但是两者的 ***sql*** 语法其实是有所去别的，这个区分就交给 ***Hibernate*** 去做了，这句话告诉 ***Hibernate*** 底层使用什么数据库，***Hibernate*** 才知道应该用什么样的 **“ 方言 ”** 去对话。

#### （8）TestHibernate.java

***HIbernate*** 的基本逻辑操作如下：

1. 获取 ***SessionFactory***；
2. 通过 ***SessionFactory*** 获取一个 ***Session***；
3. 在 ***Session*** 基础上开启一个事务；
4. 进行增删查改操作；
5. 通过调用 ***Session*** 的 ***save()*** 方法把对象保存到数据库；
6. 提交事务；
7. 关闭 ***Session***；
8. 关闭 ***SessionFactory***；

在整个操作流程中，Hibernate对象有3中状态：***瞬时态（Transient）***、***持久态（Persistent）***和 ***脱管态（Detached）***。

- ***new*** 了一个 ***Product***，但是在数据库中没有对应的记录，此时的对象是瞬时态；
- 对象通过  ***Session*** 的 ***save*** 把数据保存在数据库中，也就和 ***Session*** 之间产生了联系，此时是持久态；
- ***Session*** 关闭了，对象在数据库中有对应的数据，但是和 ***Session*** 失去联系，相当于脱离了管理，即脱管态；

##### 代码示例：

###### （1）获取/关闭SessionFactory/Session

```java
SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
Session session = sessionFactory.openSession();
session.beginTransaction();
//doSomeThing();
session.getTransaction().commit();
session.close();
sessionFactory.close();
```

###### （2）插入Product数据

```java
for(int index = 0; index < 10; index++) {
	Product p = new Product();
	p.setName("iphone" + index);
	p.setPrice(index);
	session.save(p);
}
```

###### （3）根据条件获取/修改/删除数据

```java
Product product =(Product) session.get(Product.class, 6);
product.setName("iphone-modified");      
session.update(product);
//session.delete(product);//删除
```

###### （4）在Product中插入Category

```java
Category category = new Category(); 
category.setName("c1"); 
session.save(category);
Product product = (Product)session.get(Product.class, 8);
product.setCategory(category); 
Product product2 = (Product)session.get(Product.class, 7); 
product2.setCategory(category);
session.update(product);
```

###### （5）查找哪些 Product属于某个 Category

```java
Category category = (Category)session.get(Category.class, 2); 
Set<Product> pSet = category.getProducts(); 
for(Product product : pSet) {
	System.out.println(product.getName());
}
```

###### （6）多个 User购买了同一个个 Product

```java
Product p1 = (Product) session.get(Product.class, 3); 
p1.setUsers(users);
session.save(p1);
```

###### （7）多种方式查询

**HQL**查询：

***HQL(Hibernate Query Language)*** 是 ***Hibernate*** 专门用于查询数据的语句

```java
String name = "iphone"; 
Query query = session.createQuery("from Product p where p.name like ?"); 
query.setString(0, "%" + name + "%"); 
List<Product> psList = query.list(); 
```

***Criteria*** 查询：

完全看不出 ***HQL*** 或者 ***SQL*** 语句的痕迹

```java
String name = "iphone"; 
Criteria criteria = session.createCriteria(Product.class);
criteria.add(Restrictions.like("name", "%" + name + "%")); 
List<Product> psList = criteria.list(); 
```

***SQL*** 语句：

```java
String name = "iphone"; 
String sql = "select * from product_ p where p.name like '%" + name + "%'"; 
Query query = session.createSQLQuery(sql); 
List<Object[]> list = query.list(); 
```



