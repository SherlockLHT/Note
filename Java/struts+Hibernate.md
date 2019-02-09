## 说明

- ***structs*** 的作用是拦截***url*** 请求，分配给不同的***action*** 处理；
- ***hibernate*** 的作用是封装数据库操作，避免 ***sql*** 语句拼接；
- ***struts+hibernate*** 即将MVC中的M层交给 ***hibernate*** 处理；

## 代码示例：

示例使用 ***Eclipse*** 开发，以简单的 ***url*** 拦截和数据库增删查改等功能完成说明。

### 需求说明：

1. 通过页面实现 ***Product*** 数据的增删查改；
2. ***Product*** 有 ***name*** 和 ***price*** 属性；

### 代码结构：

1. ***hibernate.cfg.xml***，数据库的配置；
2. ***Product.java***，作为***bean***，保存 ***name*** 和 ***price*** 数据，以及 ***lowerLimit*** 和 ***upperLimit*** 作为查询时的 ***price*** 上下限；
3. ***Product.hbm.xml***，***Product*** 属性和数据库的映射配置；
4. ***ProductDAO.java***，封装***Product***的增删查改操作，操作数据库调用该类即可；
5. ***web.xml***， ***struts*** 的配置；
6. ***struts.xml***，***struts*** 的 ***url*** 和 ***action*** 映射和 ***result*** 配置；
7. ***ProductAction.java***，***struts***的 ***action***，***struts***拦截 ***url*** 之后，分配给指定 ***action***；
8. 一些 ***html*** 文件，前端页面；

### 代码：

##### （1）hibernate.xml

​	配置数据库，位于***src*** 目录下，略。

##### （2）Product.java

​	***product*** 的 ***bean***，有***name***、***price***、***lowerLimit*** 和 ***upperLimit*** 四个属性以及相应的 ***getter*** 和 ***setter***，略；

##### （3）Product.hbm.xml

​	***product*** 需要存入数据库的属性和数据库列对应的配置，和 ***Product.java*** 同级目录，略；

##### （4）ProductDAO.java

​	封装数据库增删查改操作

```java
public class ProductDAO {
    /*
     * @brief 增
     */
    public void addProduct(Product product) {
        SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
        Session session = sessionFactory.openSession();
        session.beginTransaction();
        
        session.save(product);
        
        session.getTransaction().commit();
        session.close();
        sessionFactory.close();
    }
    
    /*
     * @brief 删
     */
    public void deleteProduct(int id) {
        SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
        Session session = sessionFactory.openSession();
        session.beginTransaction();
        
        Product product = (Product)session.get(Product.class, id);
        
        session.delete(product);
        
        session.getTransaction().commit();
        session.close();
        sessionFactory.close();
    }
    
    /*
     * @brief 查
     */
    public List<Product> queryProducts(String name, float priceLowerLimit, float priceUpperLimit){
        SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
        Session session = sessionFactory.openSession();
        session.beginTransaction();
        
        String hqlString = "from Product product where product.name like ? "
                + "and product.price >= ? "
                + "and product.price <= ?";
        Query query = session.createQuery(hqlString);
        query.setParameter(0, "%" + name + "%");
        query.setParameter(1, priceLowerLimit);
        query.setParameter(2, priceUpperLimit);
        
        List<Product> liseProducts = query.list();
        
        System.out.println(query.getQueryString());
        
        session.close();
        sessionFactory.close();
        return liseProducts;
    }
    
    /*
     * @brief 改
     */
    public boolean updateProducts(int id, Product product) {
        SessionFactory sessionFactory = new Configuration().configure().buildSessionFactory();
        Session session = sessionFactory.openSession();
        session.beginTransaction();
        
        Product oldProduct = (Product) session.get(Product.class, id);
        if (oldProduct == null) {
            return false;
        }
        
        oldProduct.setName(product.getName());
        oldProduct.setPrice(product.getPrice());
        
        session.update(oldProduct);
        
        session.getTransaction().commit();
        session.close();
        sessionFactory.close();
        return true;
    }
}
```

##### （5）web.xml

​	***struts***配置，位于***WEB-INF*** 目录下，略。

##### （6）struts.xml

​	位于***src*** 目录下；

​	***url*** 映射配置等；

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE struts PUBLIC
    "-//Apache Software Foundation//DTD Struts Configuration 2.0//EN"
    "http://struts.apache.org/dtds/struts-2.0.dtd">
 
<struts>
    <constant name="struts.i18n.encoding" value="UTF-8"></constant>
    <package name="basicstruts" extends="struts-default">
        <action name="index" class="com.how2java.action.ProductAction" method="showIndex">
            <result name="index">index.html</result>
        </action>
        <action name="query" class="com.how2java.action.ProductAction" method="query">
            <result name="index">index.html</result>
        </action>
        <action name="insert" class="com.how2java.action.ProductAction" method="insert">
            <result name="pass">pass.html</result>
            <result name="fail">fail.html</result>
        </action>
    </package>
</struts>
```

##### （7）ProductAction.java

​	***struts***拦截***url***后，***action*** 接受***struts*** 的分配。

```java
public class ProductAction extends ActionSupport{
    private Product product;
    private ProductDAO productDAO = new ProductDAO();
    
    public Product getProduct() {
        return product;
    }
    public void setProduct(Product product) {
        this.product = product;
    }
    
    public String showIndex() {
        return "index";
    }
    public String insert() {
        System.out.println(product.getName());
        System.out.println(product.getPrice());
        productDAO.addProduct(product);
        return "pass";
    }
    public String query() {
        System.out.println(product.getName());//获取参数
        System.out.println(product.getLowerLimit());
        System.out.println(product.getUpperLimit());
        List<Product> listPList = productDAO.queryProducts(product.getName(), product.getLowerLimit(), product.getUpperLimit());
        for (Product product : listPList) {
            System.out.println(product.getName());
        }
        
        return "index";
    }
}
```

##### （8）html前端文件

```html
//index.html
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>index</title>
    <style>
        label{
            display: block;
        }
    </style>
</head>
<body>
    <form action="insert" method="post">
        <label>Name:
            <input type="text" value="name_default" name="product.name">
        </label>
        <label>Price:
            <input type="number" value="0" name="product.price">
        </label>
        <input type="submit" value="插入">
    </form>
    <hr>
    <form action="query" method="post">
        <label>Name:
            <input type="text" name="product.name">
        </label>
        <label>
            Price Lower Limit:
            <input type="number" value="0" name="product.lowerLimit">
        </label>
        <label>
            Price Upper Limit:
            <input type="number" value="99999" name="product.upperLimit">
        </label>
        <input type="submit" value="查询">
    </form>
    <hr>
    <forma>
        <label>Name:
            <input type="text" name="product.name">
        </label>
        <label>New Name:
            <input type="text" name="newName">
        </label>
        <input type="submit" value="修改">
    </forma>
</body>
</html>
```

```html
//pass.html
<h1 style="color:green">Pass</h1>
```

```html
//fail.html
<h1 style="color:red">Fail</h1>
```



注：***struts*** 的 ***action*** 如何获取参数，参考 ***action*** 获取参数的相关文章，本文不做讨论。

部分代码省略，参开相关框架介绍文章。