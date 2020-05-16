参考：https://blog.csdn.net/sinat_38259539/article/details/71799078

https://blog.csdn.net/lililuni/article/details/83449088

# 1、概述

所谓Java反射，即在Java程序运行中，能够获取使用任意一个类的成员

而Java生态中的各种框架的灵魂就是反射机制

# 2、反射的简单使用

先来了解一下Java的反射的使用

## 2.1、使用反射获取类

有三种方式获取 Class 对象：

1. 通过 Object 的 getClass() 方法；
2. 通过类的静态属性 class，每个数据类型都有，包括基本数据类型；
3. 通过 Class 的静态方法 forName()；

示例：

**测试类**

```java
package com.study;
public class Product {
    private String name;
    private double price;
    //getter/setter忽略
}
```

**法一：**

```java
package com.company;
import com.study.Product;
public class Main {
    public static void main(String[] args) {
        Product product = new Product();
        Class productClass = product.getClass();
        System.out.println(productClass.getName());
    }
}
```

**法二：**

```java
package com.company;
import com.study.Product;
public class Main {
    public static void main(String[] args) {
        Class productClass2 = Product.class;
        System.out.println(productClass2.getName());
    }
}
```

**法三：**

```java
package com.company;
public class Main {
    public static void main(String[] args) throws ClassNotFoundException {
        Class productClass3 = Class.forName("com.study.Product");
        System.out.println(productClass3.getName());
    }
}
```

以上三种方法都会输出：

```java
com.study.Product
```
可以使用以下代码测试上述三种对象是都是同一个：
```java
System.out.println(productClass==productClass2);	//true
System.out.println(productClass3==productClass2);	//true
```

由上述代码可以看出几点：

1. 一个类只会有一个Class对象，即使使用了不同的方法，获得的Class对象只有一个；
2. 方法一和方法二因为使用了类，所以需要导入包，依赖性强；
3. 方法一已经new了对象，还要反射，多此一举；
4. 方法三使用包名+类名拼成的字符串，灵活，常用；

## 2.2、使用返回获取构造函数创建对象

**测试类：**

```java
package com.study;

public class Product {
    private String name;
    private double price;

    public Product(){
        System.out.println("Constructor");
    }
    public Product(int value){
        System.out.println(String.format("Constructor %d", value));
    }
    protected Product(String name){
        System.out.println("protected Constructor with name");
        this.name = name;
    }
    private Product(String name, double price){
        System.out.println("private Constructor with name and price");
        this.name = name;
        this.price = price;
    }
	//getter和setter忽略
}
```

示例代码：

```java
package com.company;
import com.study.Product;
import java.lang.reflect.Constructor;

public class Main {
    public static void main(String[] args) throws Exception  {
        Class productClass = Class.forName("com.study.Product");

        System.out.println("获取公有构造函数:");
        Constructor[] constructors = productClass.getConstructors();
        for(Constructor constructor : constructors){
            System.out.println(constructor);
        }
        System.out.println("获取所有构造函数:");
        Constructor[] allConstructors = productClass.getDeclaredConstructors();
        for(Constructor constructor : allConstructors){
            System.out.println(constructor);
        }
        System.out.println("获取公有的无参的构造函数:");
        Constructor constructor = productClass.getConstructor(null);
        System.out.println(constructor);

        //使用公有构造函数
        Object productObject = constructor.newInstance();
        System.out.println(productObject);

        constructor = productClass.getDeclaredConstructor(String.class, double.class);
        constructor.setAccessible(true);    //权限
        Product product = (Product)constructor.newInstance("1#商品", 1.23);
        System.out.println(product.getName());
    }
}
```

输出：

```java
获取公有构造函数:
public com.study.Product(int)
public com.study.Product()
获取所有构造函数:
private com.study.Product(java.lang.String,double)
protected com.study.Product(java.lang.String)
public com.study.Product(int)
public com.study.Product()
获取公有的无参的构造函数:
public com.study.Product()
Constructor
com.study.Product@1540e19d
private Constructor with name and price
1#商品
```

## 2.3、获取成员属性并修改

- 使用 **getField()** 方法获取 public 属性；
- 使用 **getDeclaredField()** 方法获取所有属性；

以2.2中的代码测试类为例

```java
Class productClass = Class.forName("com.study.Product");
        
Product product = new Product();
System.out.println(product.getName());

Field name = productClass.getDeclaredField("name");
name.setAccessible(true);	//因为是private，所以使用该方法忽略权限
name.set(product, "2#商品");
System.out.println(product.getName());
```

输出：

```
Constructor
null
2#商品
```

## 2.4、获取成员方法

- 使用 **getMethod()** 方法获取成员；
- 使用 **invoke()** 方法执行；

以2.2中的代码测试类为例

```java
Class productClass = Class.forName("com.study.Product");

Product product = new Product();
System.out.println(product.getName());

Method method = productClass.getMethod("setName", String.class);
method.invoke(product, "3#商品");

System.out.println(product.getName());
```

输出：

```
Constructor
null
3#商品
```

## 2.5、获取main方法

```java
Class productClass = Class.forName("com.study.Product");

Method method = productClass.getMethod("main", String[].class);
method.invoke(null, (Object) new String[]{"a", "b"});
```

# 3、反射的进阶用法

上面列出了反射的一些简单的用法，利用这些简单的用法就可以实现很多复杂的功能，spring 这类框架的的灵魂就是反射

普通的方式new对象、调用函数都需要知道类的内容，而且都是固定的，即代码都是写死的。而从上面的例子中可以看出，获取对象、方法等可以通过函数、方法的字符串名称来实现，如此，我们只需要在程序外控制这些字符串就可以不修改代码从而很灵活地调用方法

## 3.1、通过程序外部配置动态运行

**示例：**

使用配置文件控制new出来的对象

```java
package com.study;
public class Teacher {}
```

```java
package com.study;
public class Student {}
```
配置文件：
```
class: com.study.Teacher
```

测试类：

```java
//读取配置文件到变量clssName中
Class class = Class.forName(className);
```

如上，class 对象会根据配置文件的改变而改变

## 3.2、通过反射越过泛型检查

泛型是在编译期间作用的，如果泛型检查不过，则编译会失败，例如，想要在一个List中既保存字符串，又保存数字，就会编译出错，但是使用反射就能够实现

示例：

```java
List<String> list = new ArrayList<>();
list.add("aaa");
list.add("bbb");
//list.add(111);	//这里是不允许的
Class listClass = list.getClass();
Method method = listClass.getMethod("add", Object.class);
method.invoke(list, 111);
method.invoke(list, 222);

for(Object obj : list){
	System.out.println(obj);
}
```

如上，list指定了 String 类型作为元素成员的类型，添加整型就会出错，但是如果使用反射就可以越过这种检查

# 4、反射原理

了解反射的一些使用之后，在回过头来看反射的原理就比较简单了。

众所周知，Java中，一切皆对象，哪怕是 Java 代码编译之后的 class 也是一个对象。我们再次熟悉一下class的加载过程

1. 首先使用 javac 指令将 product.java 文件编译成 product.class 文件，class是字节码文件，由 JVM 进行解释和运行；
2. JVM从磁盘中找到 product.class 文件，并加载到JVM内存中；
3. JVM根据 product.class 创建 class 对象，一个 .class 必须并只能创建一个 class 对象；
4. JVM 根据 product 类的各个成员都创建各自的 class 对象，每个成员有且只有一个class 对象；
5. 使用反射时调用 Class 类的方法获取这些对象；

# 总结

本文只介绍一些简单的反射知识，关于反射更加巧妙的用法可以自行阅读相关框架的源码