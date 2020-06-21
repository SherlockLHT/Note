参考：https://www.cnblogs.com/demingblog/p/10874753.html#

# 1、说明

SpringSecurity 用于spring项目中安全控制，和shiro作用相同

springsecurity 功能强大，并且可以和spring项目无缝集成，但是和 spring 框架的绑定太深，并且较为复杂，用有些人的话说就是，在看大佬玩设计模式

相比之下，shiro 使用简单，更容易上手，更独立

本文简单介绍 springsecurity 的入门用法，并不做深入研究

springsecurity 框架中有有过一次升级，springboot1.x 和 springboot2.x的版本用法完全不同，本文主要以在 springboot2.x 版本中的使用为主

官方文档： https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-2.0-Migration-Guide#security

# 2、spring security的基本认识

# 2.1、 创建不受保护的应用

创建一个 springboot 应用，编写一个controller，其他都用默认设置

```java
@Controller
public class ProductController {
    @RequestMapping("/hello")
    @ResponseBody
    public String helo(){
        return "hello, springsecurity";
    }
}
```

如上代码，运行后，访问 127.0.0.0:8080/hello 即可看到 hello, springsecurity 文字

## 2.2、使用spring security保护应用

2.1 中可以自由访问，现在，我们想要指定用户访问

添加 spring security 库

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

运行后，再次访问时，就需要登陆，springboot1.x 会弹出一个http-batic的认证框，点击取消之后，就会包以下错误

```
There was an unexpected error (type=Unauthorized, status=401).
```

spring security 的默认用户名是 user，密码会在项目启动的时候生成，可以在控制台看到

1.x 版本可以使用配置文件来关闭认证、配置默认用户名密码等

2.x 版本的sping security 会跳转到一个比较美观的登陆页面，并且取消了配置文件中的一些配置，具体可以参考官方文档

## 2.3、spring security的登陆页面

跳转的登陆页面我们可以看到，如下，我删掉了一些代码，只留下关键

```html
<form class="form-signin" method="post" action="/login">
	<h2 class="form-signin-heading">Please sign in</h2>
    <p>
        <label for="username" class="sr-only">Username</label>
        <input type="text" id="username" name="username" 
               class="form-	control" placeholder="Username" 
               required autofocus>
    </p>
    <p>
        <label for="password" class="sr-only">Password</label>
        <input type="password" id="password" 
               name="password" class="form-control" 
               placeholder="Password" required>
    </p>
    <input name="_csrf" type="hidden" 
           value="4616e58d-5a8e-4054-a0b4-d50b9ea911a0" />
    <button class="btn btn-lg btn-primary btn-block" 
            type="submit">Sign in</button>
</form>
```

从代码中，我们可以看到以下几点：

1. form 表单提交的数据action为 /login，这个是 spring security 提供的；
2. form 表单提交的数据类型有三种，分别是：username、password 和 _csrf；

## 2.4、角色权限控制访问

项目中，我们需要指定的角色权限访问特定的资源

假设现在有两个角色：

1. ADMIN 角色访问所有资源；
2. USER 角色只能访问指定资源；

首先新增 **controller**

```java
@Controller
@RequestMapping("/admin")
public class ProductController {
    @RequestMapping("/hello")
    @ResponseBody
    public String helo(){
        return "hello, springsecurity";
    }
}
```

```java
@Controller
@RequestMapping("/product")
public class ProductController {
    @RequestMapping("/index")
    @ResponseBody
    public String index(){
        return "index, springsecurity";
    }
}
```

上面两个 controller 分别需要使用 /admin/hello 和 /product/index 来访问

然后，创建两个用户，并设置角色，都保存在内存中

```java
@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity httpSecurity) 
        throws Exception {
        httpSecurity.authorizeRequests()
                .antMatchers("/admin/**").hasRole("ADMIN")
                .antMatchers("/product/**").hasRole("USER")
                .anyRequest().authenticated()
                .and()
                .formLogin().and()
                .httpBasic();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) 
        throws Exception {
        auth.inMemoryAuthentication()
                .withUser("admin")
                .password("{noop}admin")
                .roles("ADMIN", "USER")
                .and()
                .withUser("user")
                .password("{noop}user")
                .roles("USER");
    }
}
```

如上，WebSecurityConfigurerAdapter 是 spring security 的类，**configure(AuthenticationManagerBuilder auth)** 方法添加用户，设置权限，注意，这个是2.x的写法，1.x不同

**(HttpSecurity httpSecurity)** 方法设置角色的访问资源控制

如上代码中表示：

1. admin/user 的密码为 admin/user；
2. admin 属于 ADMIN 和 USER 角色，user 属于 USER 角色；
3. ADMIN 角色只能访问 /product/** 路径资源，ADMIN 可以访问 /admin/** 路径资源

没有权限的时候，报错如下：

```
There was an unexpected error (type=Forbidden, status=403).
```

## 2.5、获取当前登陆的用户信息

当使用 spring security 登陆，并访问了controller 之后，controller 就可以获取当前登陆的用户的信息

```java
@Controller
public class ProductController {
    @RequestMapping("/hello")
    @ResponseBody
    public String helo(){
        String currentUser;
        Object object = SecurityContextHolder
            				.getContext()
            				.getAuthentication()
            				.getPrincipal();
        if(object instanceof UserDetails){
            currentUser = ((UserDetails) object).getUsername();
        } else {
            currentUser = object.toString();
        }
        return String.format("Hello, %s", currentUser);
    }
}
```

## 2.6 小结

至此，我们已经对 spring security 有了一个大致的认识，也能够利用其进行业务的开发，但是spring security 的功能强大，想要用好，好需要深入学习，了解其核心概念。

