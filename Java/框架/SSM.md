[TOC]

## 说明

- **Spring** + **SpringMVC** + **Mybatis** 三大框架的整合；

## 示例

### 需求

1. **Category** 有 **id** 和 **name** 两个属性；
2. 通过前端 **html** 页面完成对数据库内存储的 **Category** 数据的 **CURD**；

### 代码结构

1. **web.xml**，**WEB-INF** 目录下，有两个作用：
   1. 通过 **ContextLoaderListener** 在 **web app** 启动的时候，获取 **applicationContext.xml** 文件名，并进行 **spring** 的初始化工作；
   2. 通过 **DispatcherServlet** 拦截访问请求，即 **springMVC** 的工作机制；
2. **applicationContext.xml**，**spring** 的配置文件，位于 **src** 目录下，有四大作用：
   1. 管理 **service** 的生命周期；
   2. 配置数据库；
   3. 扫描 **mapper**，管理其声明周期；
   4. 扫描 **sql** 语句；
3. **springMVC.xml**，**springMVC** 的配置文件，管理 **control** 的生命周期；
4. **Category.java**，实体类；
5. **CategoryMapper.java**，数据库操作接口；
6. **CategoryMapper.xml**，和**CategoryMapper.java**同级目录下，配置复杂 **sql** 语句；
7. **CategoryService.java**，**service** 层接口；
8. **CategoryServiceImpl.java**，**service** 层实现类，实现接口，负责逻辑部分；
9. **CategoryController.java**，**C** 层，负责收发请求；
10. 一些前端文件；

### 代码

#### （1）web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:web="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" version="2.5">

    <!-- spring -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>
            org.springframework.web.context.ContextLoaderListener
        </listener-class>
    </listener>
    
    <!-- springmvc核心,分发servlet -->
    <servlet>
        <servlet-name>mvc-dispatcher</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- spring mvc配置 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springMVC.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>mvc-dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>
            org.springframework.web.filter.CharacterEncodingFilter
        </filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

具体配置参见各自框架文章的说明。

#### （2）applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx" xmlns:jdbc="http://www.springframework.org/schema/jdbc"
    xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xsi:schemaLocation="
     http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd
     http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
     http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc-3.0.xsd
     http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
     http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
     http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!-- 通过注解，将service的生命周期纳入spring管理 -->
    <context:annotation-config />
    <context:component-scan base-package="com.how2java.service" />
    
    <!-- 配置数据库 -->
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName">
            <value>com.mysql.jdbc.Driver</value>
        </property>
        <property name="url">
            <value>jdbc:mysql://localhost:3306/ssm?characterEncoding=UTF-8</value>
        </property>
        <property name="username">
            <value>root</value>
        </property>
        <property name="password">
            <value>12345</value>
        </property>
    </bean>
    <!-- 扫描存放sql语句的xml文件 -->
    <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
       <property name="typeAliasesPackage" value="com.how2java.pojo" />
       <property name="dataSource" ref="dataSource"/>
       <property name="mapperLocations" value="classpath:com/how2java/mapper/*.xml"/>
    </bean>
    <!-- 将数据库的mapper接口纳入spring管理 -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <property name="basePackage" value="com.how2java.mapper"/>
    </bean>
</beans>
```

#### （3）springMVC.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
    xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
        http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.2.xsd
        http://www.springframework.org/schema/aop 
        http://www.springframework.org/schema/aop/spring-aop-3.2.xsd
        http://www.springframework.org/schema/tx 
        http://www.springframework.org/schema/tx/spring-tx-3.2.xsd
        http://www.springframework.org/schema/mvc 
        http://www.springframework.org/schema/mvc/spring-mvc-3.2.xsd">

    <context:annotation-config />
    <context:component-scan base-package="com.how2java.controller">
          <context:include-filter type="annotation" 
          expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
     
    <mvc:annotation-driven />

    <mvc:default-servlet-handler />

    <bean
        class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="viewClass"
            value="org.springframework.web.servlet.view.JstlView" />
        <property name="prefix" value="/WEB-INF/html/" />
        <property name="suffix" value=".html" />
        <property name="order" value="1"/>
    </bean>
</beans>
```

**说明：**

```xml
<context:annotation-config />
<context:component-scan base-package="com.how2java.controller">
	<context:include-filter type="annotation" 
		expression="org.springframework.stereotype.Controller"/>
</context:component-scan>
```

- 扫描 **controller**，将其声明周期纳入 **spring** 管理；
- 配置 **controller** 使用注解；

```xml
<mvc:annotation-driven />
```

注解驱动，访问路径和方法可以通过注解配置 。

```xml
<mvc:default-servlet-handler />
```

设置静态页面资源，不需要再使用 **FreeMarker** 管理不同格式( **html/jsp** 等)的静态资源。

```xml
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="viewClass" value="org.springframework.web.servlet.view.JstlView" />
	<property name="prefix" value="/WEB-INF/html/" />
	<property name="suffix" value=".html" />
	<property name="order" value="1"/>
</bean>
```

配置静态视图定位的路径。

#### （4） Category.java

略

#### （5）CategoryMapper.java

```java
public interface CategoryMapper {
    public int addCategory(String name);
    
    public List<Category> getCategoriesByName(String name);
    
    public List<Category> getCategoriesPage(int start, int count);
}
```

#### （6）CategoryMapprt.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper
    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    
<mapper namespace="com.how2java.mapper.CategoryMapper">
    <insert id="addCategory" parameterType="string">
        insert into category_ (name) values (#{name})
    </insert>
    
    <select id="getCategoriesByName" parameterType="string" resultType="Category">
        select * from category_ where name like concat('%', #{name}, '%') 
    </select>
    
    <select id="getCategoriesPage" parameterType="int" resultType="Category">
        select * from category_ limit #{0}, #{1}
    </select>
</mapper>
```

#### （7）CategoryService.java

```java
public interface CategoryService {
    public void addCategory(String name);
    
    public List<Category> queryCategory(String name);
    
    public List<Category> queryPage(int start, int count);
}
```

#### （8）CategoryServiceImpl.java

```java
@Service
public class CategoryServiceImpl implements CategoryService{
    @Autowired
    CategoryMapper categoryMapper;

    @Override
    public void addCategory(String name) {
        // TODO Auto-generated method stub
        categoryMapper.addCategory(name);
    }

    @Override
    public List<Category> queryCategory(String name) {
        // TODO Auto-generated method stub
        return categoryMapper.getCategoriesByName(name);
    }

    @Override
    public List<Category> queryPage(int start, int count) {
        // TODO Auto-generated method stub
        return categoryMapper.getCategoriesPage(start, count);
    }
}
```

- 使用 **@Service** 注解标识这是一个 **Service**；
- 装配了 **mapper** 进行数据库操作；

#### （9）CategoryController.java

```java
@Controller
@RequestMapping("")
public class CategoryController {
    @Autowired
    CategoryService categoryService;
    
    @RequestMapping("index")
    public ModelAndView index() {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("index");
        return modelAndView;
    }
    
    @RequestMapping("addCategory")
    public String addCategory(String name) {
        categoryService.addCategory(name);
        return "index";
    }
    @RequestMapping("query")
    public String query() {
        return "query";
    }
    @RequestMapping("queryCategory")
    public String queryCategory(String name) {
        List<Category> categories = categoryService.queryCategory(name);
        for (Category category : categories) {
            System.out.println(category);
        }
        return "query";
    }
    
    @RequestMapping("queryCategoryPage")
    public String queryCategoryPage(int start, int count) {
        System.out.println("queryCategoryPage:" + start + "," + count);
        List<Category> categories = categoryService.queryPage(start, count);
        for (Category category : categories) {
            System.out.println(category);
        }
        return "query";
    }
}
```

- 使用 **@Controller** 注解表示这是一个控制器；
- 自动装配了 **service**，可以调用其接口；
- **@RequestMappering**，配置映射访问路径，再请求路径前面添加路径前缀；

#### （10）前端文件

**index.html：**

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert Category</title>
</head>
<body>
    <form action="addCategory" method="get">
        <label>Name
            <input type="text" name="name">
        </label>
        <input type="submit" value="Insert">
    </form>
</body>
</html>
```

**query.html：**

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Query Category</title>
</head>
<body>
    <form action="queryCategory" method="get">
        <label>Name:
            <input type="text" name="name">
        </label>
        <input type="submit" value="query">
    </form>
    <br>
    <form action="queryCategoryPage" method="post">
        <label>Satrt:
            <input type="number" name="start">
        </label>
        <label>Count:
            <input type="number" name="count">
        </label>
        <input type="submit" value="query page">
    </form>
</body>
</html>
```

**pass.html：**

```html
<h1 style="color:green">Pass</h1>
```

**fail.html：**

```html
<h1 style="color:red">fail</h1>
```



