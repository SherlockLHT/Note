[TOC]

## 说明

**spring** 负责管理对象声明周期，**mybatis**负责数据库的操作，**spring + mybatis** 则使用 **spring** 管理 **mybatis** 的数据库事务对象

## 代码示例

### 需求

1. 使用**spring**管理**mybatis***事务；
2. 完成对数据库的**CURD**操作；

### 代码结构

1. **applicationContext.xml**，**spring**的配置文件，这里还用来配置数据库，因此不再需要**mybatis**的数据库配置文件；
2. **Category.java**，**category**的实体**bean**；
3. **CategoryMapper.java**，数据库操作接口；
4. **TestSpringMybatis.java**，测试类；

### 代码

#### （1）applicationContext.xml

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
    
    <context:annotation-config/>
    
    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName">
            <value>com.mysql.jdbc.Driver</value>
        </property>
        <property name="url">
            <value>jdbc:mysql://localhost:3306/mybatis?charsetEncoding=UTF-8</value>
        </property>
        <property name="username">
            <value>root</value>
        </property>
        <property name="password">
            <value></value>
        </property>
    </bean>
     <bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="typeAliasesPackage" value="com.how2java.pojo" />
        <property name="dataSource" ref="dataSource"/>
        <property name="mapperLocations" value="classpath:com/how2java/mapper/*.xml"/>
     </bean>
     <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.how2java.mapper"/>
     </bean>
</beans>
```

**说明：**

```xml
<context:annotation-config/>
```

表示 **spring** 使用注解的方式。

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionFactoryBean">
	<property name="typeAliasesPackage" value="com.how2java.pojo" />
	<property name="dataSource" ref="dataSource"/>
	<property name="mapperLocations" value="classpath:com/how2java/mapper/*.xml"/>
</bean>
```

指定去哪里扫描 **mapper** 文件。

```xml
<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
	<property name="basePackage" value="com.how2java.mapper"/>
</bean>
```

指定扫描的 **Mapper** 类。

#### （2）Category.java

略

#### （3）CategoryMapper.java

```java
public interface CategoryMapper {
    @Insert("insert into category_ (name) values (#{name})")
    public int addCategory(String name);
    
    @Select("select * from category_ where name like concat('%', #{name}, '%')")
    public List<Category> getCategoriesByName(String name);
}
```

#### （4）TestSpringMybatis.java

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:applicationContext.xml")
public class TestSpringMybatis {
    @Autowired
    private CategoryMapper categoryMapper;
    
    @Test
    public void testAdd() {
        categoryMapper.addCategory("spring_mybatis test");
    }
    
    @Test
    public void testQuery() {
        List<Category> categories = categoryMapper.getCategoriesByName("spring");
        for (Category category : categories) {
            System.out.println(category);
        }
    }
}
```

**说明：**

