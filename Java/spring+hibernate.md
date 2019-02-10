[TOC]

## 说明

- ***spring*** 负责对象的生命周期，***hibernate*** 负责数据库的封装；

- ***spring + hibernate*** 整合的思路不是两者单纯的结合，而是使用 ***spring*** 框架的 ***HibernateTemplate*** 类；

  - 使用 ***HibernateTemplate*** 可以将***Hibernate*** 的持久层访问模块化；
  - ***HibernateTemplate*** 的使用的一个重要原因是不用直接控制事务，也不用操作 ***session*** ；

## HibernateTemplate和Session的区别

- 使用 ***Hibernate*** 时，需要获取 ***SessionFactory***，然后打开 ***Session***，开始一个事务，处理异常，提交一个事务，最后关闭事务；
- ***HibernateTemplate*** 封装了 ***Hibernate*** 的事务操作，只需要注入 ***SessionFactory*** 即可；

## 代码示例

### 需求：

1. 对 ***Category*** 进行数据库的增删查改操作，***Category*** 有属性 ***name***；
2. 使用 ***HibernateTemplate*** 进行操作，不手动进行事务操作；

### 代码结构：

#### Hibernate部分：

1. ***Category.java***，作为 ***Category*** 的 ***bean***，包含 ***name*** 属性以及相应的 ***setter/getter***；
2. ***Category.hbm.xml***，作为 ***Category*** 和数据库的映射；

#### spring部分：

1. ***applicationContext.xml***，负责普通对象的注入，以及 ***SessionFactory*** 的注入和数据库的配置；
2. ***CategoryDao.java***，负责数据库的 ***DAO*** 操作，不进行事务操作，继承 ***HibernateTemplate***；
3. ***TestSpringHibernate.java***，测试类；

### 代码：

#### （1）Category.java

​	略。

#### （2）Category.hbm.xml

​	略。

#### （3） applicationContext.xml

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
   
   <bean name="category" class="com.how2java.pojo.Category">
        <property name="name" value="category_test"/>
   </bean>
   
   <bean name="categoryDao" class="com.how2java.dao.CategoryDao">
        <property name="sessionFactory" ref="sf" />
   </bean>
   <bean name="sf" class="org.springframework.orm.hibernate3.LocalSessionFactoryBean">
        <property name="dataSource" ref="ds"/>
        <property name="mappingResources">
            <list>
                <value>com/how2java/pojo/Category.hbm.xml</value>
            </list>
        </property>
        <property name="hibernateProperties">
            <value>
                hibernate.dialect=org.hibernate.dialect.MySQLDialect
                hibernate.show_sql=true
                hibernate.hbm2ddl.auto=update
            </value>
        </property>
   </bean>
   <bean name="ds" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
        <property name="url" value="jdbc:mysql://localhost:3306/spring_hibernate?characterEncoding=UTF-8"/>
        <property name="username" value="root"/>
        <property name="password" value=""/>
   </bean>
</beans>

```

#### （4） CategoryDao.java

```java
public class CategoryDao extends HibernateTemplate {
    
}
```

#### （5）TestSpringHibernate.java

```java
public class TestSpringHibernate {
    //@Test
    public void testDao() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        Category category = (Category)applicationContext.getBean("category");
        System.out.println(category.getName());
    }
    //@Test
    public void testOP() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        CategoryDao categoryDao = (CategoryDao) applicationContext.getBean("categoryDao");
        Category category2 = new Category();
        category2.setName("category test 2");
        //插入
        categoryDao.save(category2);
        //获取
        Category category = (Category)categoryDao.get(Category.class, 2);
        System.out.println(category.getName());
        
        category.setName("category update");
        categoryDao.update(category);
        
        categoryDao.delete(category);
    }
    //@Test
    public void forInsert() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        CategoryDao categoryDao = (CategoryDao) applicationContext.getBean("categoryDao");
        for(int index = 0; index < 20; index++) {
            String name = "category test" + index;
            Category category = new Category();
            category.setName(name);
            
            categoryDao.save(category);
        }
    }
    //@Test
    public void testPaging() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        CategoryDao categoryDao = (CategoryDao) applicationContext.getBean("categoryDao");
        
        DetachedCriteria detachedCriteria = DetachedCriteria.forClass(Category.class);
        int start = 23;
        int count = 5;
        List<Category> categories = categoryDao.findByCriteria(detachedCriteria, start, count);
        for (Category category : categories) {
            System.out.println(category.getName());
        }
    }
    //@Test
    public void testCount() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        CategoryDao categoryDao = (CategoryDao) applicationContext.getBean("categoryDao");
        
        List<Long> counts = categoryDao.find("select count(*) from Category c");
        long count = counts.get(0);
        System.out.println(count);
    }
    @Test
    public void fuzzyQuery() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
        CategoryDao categoryDao = (CategoryDao) applicationContext.getBean("categoryDao");
        //使用HQL进行模糊查询
        List<Category> categories = categoryDao.find("from Category c where c.name like ?", "%10%");
        for (Category category : categories) {
            System.out.println(category.getName());
        }
        //使用criteria进行模糊查询
        DetachedCriteria detachedCriteria = DetachedCriteria.forClass(Category.class);
        detachedCriteria.add(Restrictions.like("name", "%10%"));
        categories = categoryDao.findByCriteria(detachedCriteria);
        for (Category category : categories) {
            System.out.println(category.getName());
        }
    }
}
```



