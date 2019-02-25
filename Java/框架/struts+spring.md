[TOC]

## 说明

- ***struts*** 的作用是拦截 ***url***，并分配给相应的 ***action*** ；
- ***spring*** 负责对象的生命周期，而这里的主要对象是 ***action***；
- ***struts + spring*** 的思路是将 ***action*** 生命周期，由原来的 ***struts*** 管理交由 ***spring***；
  - ***action*** 由原来的 ***struts*** 的 ***struts.xml*** 管理交由 ***spring*** 的 ***applicationContext.xml*** 管理；
  - ***url*** 映射关系仍然由 ***struts.xml*** 管理；

## 代码示例

### 需求：

1. 前端页面提交 ***form*** 数据；
2. 后端 ***action*** 获取提交的数据，并跳转页面；
3. ***action*** 交于 ***spring*** 的 ***applicationContext.xml*** 管理；
4. ***url*** 映射关系由 ***struts.xml*** 配置；

### 代码结构：

#### struts部分：

1. ***ProductAction.java***，***struts*** 的 ***action*** 管理；
2. ***struts.xml***，负责拦截 ***url***，并分配给 ***action***，并指定 ***objectFactory*** 对象工厂为 ***spring***，即 ***action*** 的创建交由 ***spring*** 进行；
3. ***web.xml***，配置 ***struts***；
4. 一些 ***HTML*** 前端文件，位于 ***WebContent*** 目录下；

#### spring部分：

1. ***applicationContext.xml***，负责 ***action*** 的生命周期管理；

### 代码：

#### （1）ProductionAction.java

```java
public class ProductAction {
    private String name;
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String show() {
        return "show";
    }
    public String submit() {
        System.out.println(name);
        return "pass";
    }
}
```

#### （2）struts.xml

- 负责 ***url*** 分配给 ***action***；
- 负责指定 ***objectFactory*** 对象工厂为 ***spring***；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">
 
<struts>
  <constant name="struts.i18n.encoding" value="UTF-8"/>
  <!-- 指定objectFactory对象工厂为spring -->
  <constant name="struts.objectFactory" value="spring"/>
   
  <package name="basicstruts" extends="struts-default"> 
  	<action name="index" class="productActionBean" method="show">
      <result name="show">index.html</result>
  	</action>
  	<action name="submit" class="productActionBean" method="submit">
      <result name="pass">pass.html</result>
      <result name="fail">fail.html</result>
  	</action>
  </package>
</struts>
```

说明：

```xml
<constant name="struts.objectFactory" value="spring"/>
```

负责指定 ***objectFactory*** 对象工厂为 ***spring***。

#### （3）web.xml

​	略。

#### （4）前端html文件

```html
//index.html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
</head>
<body>
    <form action="submit" method="post">
        <label>Name:
            <input type="text" name="name">
        </label>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

```html
//pass.html
<h1 style="color:green">Success</h1>
```

```html
//fail.html
<h1 style="color:green">Fail</h1>
```

#### （5）applicationContext.xml

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
  
    <bean name="productActionBean" class="com.how2java.action.ProductAction" />
</beans>
```





