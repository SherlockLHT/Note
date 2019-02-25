[TOC]


## 说明

可以看成是 ***struts*** 和 ***spring*** 的结合。

## 示例1 - 使用xml配置

### （1）需求说明

1. 通过 ***url*** 访问页面；

### （2）代码结构

1. ***web.xml***，位于 ***WEB-INF*** 目录下，配置 ***springMVC*** 的入口 ***DispatcherServlet***，把所有的请求都提交到 ***servlet***；
2. ***IndexController.java***，控制类，实现接口 ***Controller***，实现 ***handleRequest()*** 方法处理请求；
3. ***springmvc-servlet.xml***，和 ***web.xml*** 同级目录，文件名取决于 ***web.xml*** 中的配置；
4. 一些前端文件，位于 ***WebContext/WEB-INF/page*** 目录下，该目录可以配置；

### （3）代码

#### 1、web.xml

这里的 ***servlet-name*** 的值（这里是 ***springmvc*** ）接收 ***url*** 请求的 ***servlet***，决定第二项文件名。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
  <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>  
</web-app>
```

#### 2、IndexControl.java

控制类，实现 ***Controller*** 接口，提供方法 ***handleRequest*** 方法处理请求。

使用 ***ModelAndView*** 对象将模型和视图结合，这句表明，视图是 ***indexJsp***，模型数据是 ***message***，内容是 ***Hello Spring MVC***；

关于视图的路径，由 ***servlet*** 配置文件决定。

```java
public class IndexController implements Controller {
    @override
	public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
		System.out.println("function");
        ModelAndView modelAndView = new ModelAndView("indexJsp");
		modelAndView.addObject("message", "Hello Spring MVC");
		return modelAndView;
	}
}
```

#### 3、springmvc-servlet.xml

作为 ***web.xml*** 中配置的接收请求的 ***servlet*** 的配置文件。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context         
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/page/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    <bean id="simpleUrlHandlerMapping"
        class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
        <property name="mappings">
            <props>
                <prop key="/index">indexController</prop>
            </props>
        </property>
    </bean>
    <bean id="indexController" class="controller.IndexController" />
</beans>
```

##### springmvc-servlet.xml说明：

```xml 
<bean id="simpleUrlHandlerMapping"
       class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	<property name="mappings">
		<props>
			<prop key="/index">indexController</prop>
		</props>
	</property>
</bean>
<bean id="indexController" class="controller.IndexController" />
```

这表明 ***/index*** 请求会交给 ***id=indexController*** 的 ***bean*** 处理。

```xml
<bean id="viewResolver" 			    class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/page/"/>
	<property name="suffix" value=".jsp"/>
</bean>
```

这是视图定位，默认的项目路径为 ***WebContent*** 目录，若需要跳转前端页面，则 ***controller*** 类就需要传入相对路径，就会很长，这里先将静态页面的路径前缀和后缀配置好，***controller*** 传入的是 ***indexJsp***，这里就会拼接成 ***/WEB-INF/page/indexJsp.jsp***。

最后启动容器，访问 ***127.0.0.1:8080/springmvc/index*** 即可访问页面。

## 示例2 - 使用注解

### （1）需求

1. 使用注解法完成示 **例1** 中的功能；
2. 添加访问静态 ***html*** 页面的方法；
3. 添加客户端跳转功能；
4. 添加 ***Session*** 功能，计算访问一个页面的次数；
5. 添加提交表单参数功能，支持中文；

### （2）代码结构

1. ***web.xml***，位于 ***WEB-INF*** 目录下，配置 ***springMVC*** 的入口 ***DispatcherServlet***，把所有的请求都提交到 ***servlet***；
2. ***ProductController.java***，控制类，实现接口 ***Controller***，实现 ***handleRequest()*** 方法处理请求；
3. ***Product.java***，***bean*** 类，内含 ***name*** 和 ***price*** 属性及其 ***getter/setter***，这里作为传递参数使用；
4. ***springmvc-servlet.xml***，和 ***web.xml*** 同级目录，文件名取决于 ***web.xml*** 中的配置；
5. ***check.jsp***，位于 ***WebContext/WEB-INF/page*** 目录下，前端文件，作为统计访问次数使用；
6. ***index.html***，前端文件，作为静态 ***html*** 页面访问和提交表单数据使用；
7. ***pass.html***，页面跳转用；

### （3）代码

#### 1、web.xml

添加 ***Filter***，设置 ***UTF-8*** 完成中文处理。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" version="3.0">
  <servlet>
        <servlet-name>springmvc</servlet-name>
        <servlet-class>
            org.springframework.web.servlet.DispatcherServlet
        </servlet-class>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springmvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    <!-- 添加filter -->
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

#### 2、ProductController.java

- 不需要再实现接口 ***Controller***；
- 在类前面加上 ***@Controller*** 注解，表示这是一个控制器；
- 在方法前加上 ***@RequestMapping("/index")*** 表示路径 ***/index*** 会映射到该方法上；
- 方法可以传入 ***HttpSession***、***HttpRequest***等参数；

```java
@Controller
public class ProductController {
    @RequestMapping("/addProduct")
    public ModelAndView add(Product product) throws Exception{
        System.out.println(product.getName());
        System.out.println(product.getPrice());
        
        ModelAndView modelAndView = new ModelAndView("pass");
        return modelAndView;
    }
    
    @RequestMapping("/jump")
    public ModelAndView jump() {
        System.out.println("function jump()");
        ModelAndView modelAndView = new ModelAndView("redirect:/index");
        return modelAndView;
    }
    
    @RequestMapping("/check")
    public ModelAndView check(HttpSession session) {
        Integer integer = (Integer)session.getAttribute("count");
        if(integer == null) {
            integer = 0;
        }
        integer++;
        session.setAttribute("count", integer);
        ModelAndView modelAndView = new ModelAndView();
        return modelAndView;
    }
}
```

#### 3、Product.java

略。

#### 4、springmvc-servlet.xml

拿掉示例1中的 ***controller*** 的 ***bean*** 配置。

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
    xmlns:context="http://www.springframework.org/schema/context"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/context         
    http://www.springframework.org/schema/context/spring-context-3.0.xsd">
    
    <context:component-scan base-package="controller" />
    
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/page/"/>
        <property name="suffix" value=".jsp"/>
        <property name="order" value="1"/>
    </bean>
    <bean id="freemarkerConfig" class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath">
            <value>/WEB-INF/page</value>
        </property>
    </bean>
    <bean id="htmlviewResolver" class="org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver">
        <property name="suffix" value=".html"/>
        <property name="order" value="0"/>
        <property name="contentType">
            <value>text/html;charset=UTF-8</value>
        </property>
    </bean>
</beans>
```

##### 说明：

```xml
<context:component-scan base-package="controller" />
```

表明从配置的包（这里是 ***controller***）里面扫描 ***@Controller*** 注解的类。

```xml
<bean id="viewResolver" 	class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<property name="prefix" value="/WEB-INF/page/"/>
	<property name="suffix" value=".jsp"/>
	<property name="order" value="1"/>
</bean>
<!-- html视图解析器 必须先配置freemarkerConfig,注意html是没有prefix前缀属性的-->
<bean id="freemarkerConfig" 	class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
	<property name="templateLoaderPath">
		<value>/WEB-INF/page</value>
	</property>
</bean>
<bean id="htmlviewResolver" class="org.springframework.web.servlet.view.freemarker.FreeMarkerViewResolver">
	<property name="suffix" value=".html"/>
	<property name="order" value="0"/>
	<property name="contentType">
		<value>text/html;charset=UTF-8</value>
	</property>
</bean>
```

- ***InternalResourceViewResolver*** 只支持 ***jsp***，想要支持 ***html*** 就需要针对 ***html*** 进行配置；
- 配置 ***html*** 视图解析器之前必须先配置 ***freemarkerConfig***；
- ***html*** 视图解析器的配置不需要 ***suffix*** 属性；
- ***order*** 属性决定优先级，值越小，优先级越高，当请求进来，先根据优先级进行查找；
- 这里设置 ***contentType*** 属性完成前端的中文显示（但是前端不能有 ***utf-8*** 设置，注释也不行，原因待查）；

#### 5、check.jsp

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8" isELIgnored="false"%>

session中记录的访问次数：${count}
```

#### 6、index.html

```html
<!DOCTYPE html>
<html>
<head>
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>提交数据</title>
</head>
<body>
    <form action="addProduct" method="post">
        <label>Name:
            <input type="text" name="name">
        </label>
        <label>Price:
            <input type="number" name="price">
        </label>
        <input type="submit" value="提交">
    </form>
</body>
</html>
```

#### 7、pass.html

```html
<h1 style="color:green">Pass</h1>
```

