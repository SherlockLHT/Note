## 简介

- ***Annotation*** 在 **JDK1.5** 之后新增，很多库都基于注解

- 相当于一种标记，**javac** 编译器可以通过反射了解标记；

#### java内置注解

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



