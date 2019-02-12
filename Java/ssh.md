[TOC]

## 说明

- ***ssh*** 由 ***Hibernate***、***struts*** 和 ***spring*** 三大框架整合而成；
- 使用 ***spring*** 的 ***HibernateTemplate*** 管理数据库事务；
- 使用 ***spring*** 的管理 ***action*** 的生命周期；
- 使用 ***struts*** 拦截 ***url***，分配给不同的 ***action***；

## 代码示例

### 需求

1. 通过 ***url*** 访问；
2. 访问数据库，读取数据，打印数据；若无数据，则插入数据，并打印数据；
3. 访问数据库结束后，显示 ***Pass*** 页面；

### 代码结构

1. ***Product.java***，***Product*** 实体类，包含 ***id***、***name*** 和 ***price*** 三个属性和 ***getter/setter***；
2. ***Product.hbm.xml***，***Product*** 和数据库的映射配置；
3. ***ProductDAO.java***，***DAO*** 部分接口；
4. ***ProductDAOImpl***，***DAO*** 操作类，继承自 ***HibernateTemplate***，并实现接口 ***ProductDAO***；
5. ***ProductService.java***，业务类接口；
6. ***ProductServiceImpl.java***，实现业务接口 ***ProductService***，内含具体业务操作，接受 ***productDAO*** 的注入；
7. ***ProductAction.java***，接收 ***struts*** 的 ***url*** 的分配，在 ***ssh*** 中，业务类提取成 ***ProductService***，***ProductAction*** 提供对 ***ProductService*** 的注入，其单纯充当 ***controller*** ；
8. ***struts.xml***，指定 ***objectFactory*** 为 ***spring***，把 ***action*** 的生命周期交给 ***spring*** 管理，并提供 ***url*** 和 ***action*** 的映射关系；
9. ***applicationContext.xml***，和单纯的 ***spring*** 不同，应放在 ***WEB-INF*** 目录下，提供对 ***action*** 的管理，以及对象的注入等；
10. ***web.xml***，配置 ***struts***，并增加 ***Context*** 监听器；
11. 一些前端文件，在 ***WebContent*** 目录下；

### 代码

#### （1）Product.java

​	***Product*** 实体类，略；

#### （2）Product.hbm.xml

​	***Product*** 和数据库的映射关系，略；

#### （3）ProductDAO.java

​	单纯的 ***DAO*** 接口。

```java
public interface ProductDAO {
    public List<Product> list();
    public void add(Product product);
}
```

#### （4）ProductDAOImpl.java

- 实现 ***ProductDAO*** 接口；
- 继承自 ***spring*** 的 ***HibernateTemplate***，控制事务；

```java
public class ProductDAOImpl extends HibernateTemplate implements ProductDAO {
    @Override
    public List<Product> list() {
        // TODO Auto-generated method stub
        return find("from Product");
    }

    @Override
    public void add(Product product) {
        // TODO Auto-generated method stub
        save(product);
    }
}
```

#### （5）ProductService.java

​	单纯的业务逻辑接口。

```java
public interface ProductService {
    public List<Product> list();
}
```

#### （6）ProductServiceImpl.java

```java
public class ProductServiceImpl implements ProductService {
    private ProductDAO productDAO;
    
    @Override
    public List<Product> list() {
        // TODO Auto-generated method stub
        List<Product> products = productDAO.list();
        if (products.isEmpty()) {
            for(int i = 0; i < 5; i++) {
                Product product = new Product();
                product.setName("procut " + i);
                productDAO.add(product);
                products.add(product);
            }
        }
        return products;
    }

    public ProductDAO getProductDAO() {
        return productDAO;
    }
    public void setProductDAO(ProductDAO productDAO) {
        this.productDAO = productDAO;
    }
}
```

#### （7）ProductAction.java

这里打印出一次 ***url*** 申请的 ***action*** 后文会用到。

```java
public class ProductAction {
    private ProductService productService;
    List<Product> products;
    
    public String list() {
        System.out.println("function list()");
        System.out.println(this);
        products = productService.list();
        return "pass";
    }
    
    public ProductService getProductService() {
        return productService;
    }
    public void setProductService(ProductService productService) {
        this.productService = productService;
    }
    public List<Product> getProducts(){
        return products;
    }
    public void setProducts(List<Product>products) {
        this.products = products;
    }
}
```

#### （8）struts.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">
    
<struts>
    <constant name="struts.i18n.encoding" value="UTF-8"/>
    <constant name="struts.objectFactory" value="spring"/>
    <package name="basicstruts" namespace="/" extends="struts-default">
        <action name="listProduct" class="productActionBean" method="list">
            <result name="pass">pass.html</result>
        </action>
    </package>
</struts>
```

#### （9）applicationContext.xml

​	***productActionBean*** 新增了一个属性 ***scope=“prototype”***，告诉 ***spring***，对于此对象的管理，使用 ***非单例模式*** ，这样上文每次访问该对象，都是不同的对象，用于并发。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="
   http://www.springframework.org/schema/beans 
   http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
   http://www.springframework.org/schema/aop 
   http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
   http://www.springframework.org/schema/tx 
   http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
   http://www.springframework.org/schema/context      
   http://www.springframework.org/schema/context/spring-context-3.0.xsd">

    <bean name="productActionBean" scope="prototype" class="com.how2java.action.ProductAction">
        <property name="productService" ref="productServiceImpl"/>
    </bean>
    <bean name="productServiceImpl" class="com.how2java.service.impl.ProductServiceImpl">
        <property name="productDAO" ref="productDAOImpl"/>
    </bean>
    <bean name="productDAOImpl" class="com.how2java.dao.impl.ProductDAOImpl">
        <property name="sessionFactory" ref="sf"/>
    </bean>
    <bean name="sf" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
        <property name="dataSource" ref="ds"/>
        <property name="mappingResources">
            <list>
                <value>com/how2java/pojo/Product.hbm.xml</value>
            </list>
        </property>
        <!-- 理论上hibernate.auto=update表示自动更新表结构，但是又是会失效 -->
        <!-- 此句可解决，原因带查 -->
        <property name="schemaUpdate">
            <value>true</value>
        </property>
        <property name="hibernateProperties">
            <value>
                hibernate.dialect=org.hibernate.dialect.MySQLDialect
                hibernate.show_sql=true
                hibernate.auto=update
            </value>
        </property>
    </bean>
    <bean name="ds" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://127.0.0.1:3306/ssh?characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value=""/>
    </bean>
</beans>
```

#### （10）web.xml

​	新增 ***Context*** 监听器。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app>
    <filter>
        <filter-name>struts2</filter-name>
        <filter-class>
         	org.apache.struts2.dispatcher.ng.filter.StrutsPrepareAndExecuteFilter
        </filter-class>
    </filter>
    <filter-mapping>
        <filter-name>struts2</filter-name>
        <dispatcher>FORWARD</dispatcher>
        <dispatcher>REQUEST</dispatcher>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
    <!-- context监听器 -->
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
</web-app>
```

#### （11）前端页面

​	略。

## 整体思路分析

以此示例代码为例。

1. 访问路径 ***127.0.0.1:8080/ssh/listProduct***，向服务端（***Tomcat***）发送请求；
2. ***Tomcat*** 解析 ***url***，从中找到一个 ***WebApplication***（可理解为项目名），这里是 ***ssh***，然后在项目里面查找 ***web.xml*** 文件；
3. ***web.xml*** 中配置过滤器，发现此 ***url*** 符合过滤条件，则拦截，交由 ***struts*** 处理其中的 ***listProduct***；
4. ***StrutsPrepareAndExecuteFilter*** 接受到地址后，查询 ***namespace***（这里是 ***/***），并和地址拼接，变成 ***/listProduct***；
5. 根据 ***struts*** 配置进入对应的 ***action***，***productActionBean*** 的 ***list*** 方法；
6. ***productActionBean*** 由 ***spring*** 根据 ***applicationContext.xml*** 配置创建；
7. ***spring*** 创建 ***productActionBean*** 的时候，注入了 ***productService***，创建 ***productService*** 的时候，注入了 ***productDAO***，以此类推；
8. ***struts*** 调用 ***ProductAction*** 的 ***list*** 方法；
9. 访问数据库；
10. 跳转页面；

![1549961133860](C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/1549961133860.png)