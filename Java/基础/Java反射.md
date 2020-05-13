参考：https://blog.csdn.net/sinat_38259539/article/details/71799078

https://blog.csdn.net/lililuni/article/details/83449088

# 1、概述



# 2、反射的使用

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
4. 方法三使用报名+类名拼成的字符串，灵活，常用；

## 2.2、使用返回获取构造函数

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
        constructor.setAccessible(true);    //忽略权限
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

