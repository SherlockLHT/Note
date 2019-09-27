>  参考：https://blog.csdn.net/briblue/article/details/73824058

[TOC]

# 一、简介

- ***Annotation*** 在 **JDK1.5** 之后新增，很多库都基于注解
- 相当于一种标记，**javac** 编译器可以通过反射了解标记；

> 对于注解，我们可以理解为标签，即对某一个类进行说明的标签

#  二、java内置注解

```java
@Deprecated		//当前代码已弃用，使用编译器会发出警告
@Override		//重载父类方法，可以检查出错误
@SuppressWarnings	//关闭编译器警告信息
```

示例：

```java
/**
 * 此类是用来演示注解(Annotation)的应用的，注解也是JDK1.5新增加的特性之一
 * JDK1.5内部提供的三种注解是：@SuppressWarnings(":deprecation")、@Deprecated、@Override
 **/
public class Annotation {
    //@SuppressWarnings(":deprecation")
    //这里就是注解，称为压缩警告，这是JDK内部自带的一个注解，一个注解就是一个类
    //在这里使用了这个注解就是创建了SuppressWarnings类的一个实例对象
    public static void main(String[] args) {
        System.runFinalizersOnExit(true);
        //The method runFinalizersOnExit(boolean) from the type System is deprecated(过时的，废弃的)
        //这里的runFinalizersOnExit()方法画了一条横线表示此方法已经过时了，不建议使用了
    }
    @Deprecated 
    //这也是JDK内部自带的一个注解，意思就是说这个方法已经废弃了，不建议使用了
    public static void sayHello(){
        System.out.println("haaa");
    }
    @Override 
    //这也是JDK1.5之后内部提供的一个注解，意思就是要重写(覆盖)JDK内部的toString()方法
    public String toString(){
        return "tets";
    }
}
```

# 三、自定义注解

##  1、自定义注解

如下，使用 ***@interface*** 关键字定义一个注解

```java
public @interface Test {
}
```
如下，使用注解
```java
@Test
public classMyTest{
}
```

**注：上例中的注解只是一种示范，其实是错的**

## 2、元注解

> - 是一种基本注解，能够用在注解上，可以理解为用在注解上的注解；
> - 注解都需要元注解的配合

元注解有以下五种：

```java
@Retention()
@Documented
@Target()
@Inherited
@Repeatable
```

### （1）@Retention()

> 从字面上理解为保留期，可以理解为声明周期
>
> 需要传参

**其取值如下：**

1. **RetentionPolicy.SOURCE**：修饰的注解只在源码阶段保留，编译是胡略；
2. **RetentionPolicy.CLASS**：修饰的注解保留到编译时，不加载到JVM中；
3. **RetentionPolicy.RUNTIME**：修饰的注解保留到程序运行的时候，加载到JVM中，程序运行时也能得到

**使用示例：**

下例中，自定义的 **Test** 注解的生命周期直到运行结束

```java
@Retention(RetentionPolicy.RUNTIME)
public @interface Test {
}
```

### （2）@Documented

> 从字面上理解为文档，即和文档有关，作用是激昂注解中的元素包含到Javadoc中去

### （3）@Target()

> 限定注解的使用场景
>
> 注解本来是可以在任意地方使用的，但是被@Target修饰过的注解，就只能用在特定的地方

**取值如下：**

1. **ElementType.ANNOTATION_TYPE**：可以用在注解上
2. **ElementType.CONSTRUCTOR**：可以用在构造函数上
3. **ElementType.FIELD**：可以用在属性上
4. **ElementType.LOCAL_VARIABLE**：可以用在局部变量上
5. **ElementType.METHOD**：可以用在方法上
6. **ElementType.PACKAGE**：可以用在方法上
7. **ElementType.PARAMETER**：可以用在方法内的参数上
8. **ElementType.TYPE**：可以用在类型上，比如类、接口、枚举

### （4）@Inherited

> 继承注解，表示使用该注解的类的此注解能够被子类继承，@Inherited是修饰注解的，表示的是指定注解能够被继承

```java
@Inherited
public @interface Test{}

@Test
public class A{}

public class B extends A{}
```

例子中，注解 **@Test** 被表示可以被继承，即，A使用@Test注解，其子类B虽然没写，但是也默认用用@Test这个注解

### （5）@Repeatable()

> 字面意思是注解可重复，Java1.8加入，可以算是新特性
>
> 使用此注解修饰的注解，可以被使用多次，即可以传递多个不同的值使用

```java
public @interface Persons{
    Person[] value();
}

@Repeatable(Persons.class)
public @interface Person{
    String role default "";
}

@Person(role="Coder")
@Person(role="Leader")
public class SuperMan{
    
}
```

上例中，@Repeatable 修饰了注解 @Person

## 3、注解属性

 - 注解可以有成员变量，不能有方法
- 成员变量可以使用“无形参的方法”形式来声明
- 注解传参使用 **value=""** 的形式，多个属性之间用 **,**(逗号) 隔开
- 注解的传参必须是基本数据类型及其数组
- 传参可以使用 **default** 指定默认值，若指定默认值，则使用时可以不传参
- 若参数只有一个，则可以直接输入参数值
- 若注解没有参数，则连括号都可以省略

```java
@Targert(ElementType.TYPE)
@Retension(RetentionPolicy.RUNTIME)
public @interface Test{
    int id();
    String message();
}

@Test(id=123, message="Test Annotatopn")
public class MyTest{
    
}
```

如上例，注解 @Test 拥有两个属性，看起来是没有形参的方法，在注解中表示属性；

```java
@Targert(ElementType.TYPE)
@Retension(RetentionPolicy.RUNTIME)
public @interface Test{
    int id() default -1;
    String message() default "None";
}

@Test()
public class MyTest{
    
}
```

上例中，因为注解的参数有默认值，则使用时可以不用传参，使用默认值

```java
@Targert(ElementType.TYPE)
@Retension(RetentionPolicy.RUNTIME)
public @interface Test{
    int id();
}

@Test(12345)
//@Test(id=12345)
public class MyTest{
    
}
```

上例中，因为注解只有一个参数，则传参时可以直接输入数值，上面两种注解的方式效果是一样的

# 四、Java预置的注解

Java内置有几个注解

## 1、@Deprecated

- **标记被修饰者已过时，若调用，则在编译时发出警告**；

## 2、@Override

标记被修饰的方法时重载的父类方法

## 3、SafeVarargs

参数类型安全注解，提醒开发者不要用参数做一些不安全的操作，它的存在会阻止编译器长生unchecked这类的警告；

Java 1.7中加入

## 4、FunctionalInterface

- **函数式接口注解**，即一个具有一个方法的普通接口；
- Java 8 中加入；







