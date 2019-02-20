[TOC]

## 说明

使用 ***xml*** 配置导致配置文件杂乱，使用注解简单明了

## 代码示例

### 需求：

使用注解的方式完成 ***Mybatis(一)使用 XML配置*** 文章中的相同需求。

### 代码结构：

1. ***mybatis-config.xml***，数据库配置文件，位于 ***src*** 目录下；
2. ***Product.java***，***product*** 的持久化 ***bean*** 文件，内含属性和 ***getter/setter***；
3. ***ProductMapper.java***，获取 ***Product*** 类型数据的 ***interface***，内含注解；
4. ***Category.java***，***category*** 的持久化 ***bean*** 文件，内含属性和 ***getter/setter***；
5. ***CategoryMapper.java***，获取 ***Category*** 类型数据的 ***interface***，内含注解；
6. ***Order.java***，***Order*** 的持久化 ***bean*** 文件，内含属性和 ***getter/setter***；
7. ***OrderMapper.java***，获取 ***Order*** 类型数据的 ***interface***，内含注解；
8. ***OrderItem.java***，***order*** 和 ***product*** 映射关系的持久化 ***bean*** 文件，内含属性和 ***getter/setter***；
9. ***OrderItemMapper.java***，获取 ***OrderItem*** 类型数据的 ***interface***，内含注解；
10. ***TestMybatisByAnnotation.java***，测试类；

### 代码：

#### （1）mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!-- 同xml配置，略 -->
    <mappers>
        <mapper class="com.how2java.mapper.CategoryMapper"/>
        <mapper class="com.how2java.mapper.ProductMapper"/>
        <mapper class="com.how2java.mapper.OrderMapper"/>
        <mapper class="com.how2java.mapper.OrderItemMapper"/>
    </mappers>
</configuration>
```

使用 ***xml*** 配置时，使用 ***resource*** 属性指定文件路径

使用注解时，使用 ***class*** 属性指定类。

#### （2）Product.java

略

#### （3）ProductMapper.java

```java
public interface ProductMapper {
    @Select("select * from product_ where cid=#{cid}")
    public List<Product> getProductsByCategoryId(int cid);
    
    @Select("select * from product_")
    @Results({
        @Result(property="category", column="cid", 
               one=@One(select="com.how2java.mapper.CategoryMapper.getCategoriesByName"))
    })
    public List<Product> getProducts();
    
    @Select("select * from product_ where id=#{id}")
    public Product getProductById(int id);
}
```

***说明：***

- ***sql*** 语句以及参数的传入等均无变化；
- 结果使用 ***@Results*** 注解；
- 结果的每个属性使用 ***@Result*** 注解；
- 通用类型属性使用 ***property*** 指定属性名， ***column*** 指定属性数据应该对应数据库的列；
- 包含自定义类型的属性需要再次调用其他接口获取数据，***one=@One*** 表示对一，***many=@Many*** 表示对多，里面使用 ***select*** 指定具体接口；
- 注解里调用其他接口需要传递参数，使用 ***column*** 传递参数；

#### （4）Category.java

略

#### （5）CategoryMapper.java

```java
public interface CategoryMapper {
    @Insert("insert into category_ (name) values (#{name})")
    public int addCategory(Category category);
    
    @Delete("delete from category_ where id=#{id}")
    public void deleteCategoryById(int id);
    
    @Select("select * from category_ where id=#{id}")
    public Category getCategoryById(int id);
    
    @Update("update category_ set name=#{name} where id=#{id}")
    public int updateCategory(Category category);
    
    @Select("select * from category_ where name like concat('%',#{name},'%')")
    @Results({
        @Result(property="id", column="id"),
        @Result(property="products", javaType=List.class, column="id",
         many=@Many(select="com.how2java.mapper.ProductMapper.getProductsByCategoryId"))
    })
    public List<Category> getCategoriesByName(String name);
}
```

**说明：**

对多时，需要指定属性的类型，一般是 ***List.class***，

#### （6）Order.java

略

#### （7）OrderMapper.java

```java
public interface OrderMapper {
    @Select("select * from order_ od inner join order_product_ op on od.id=op.oid "
            + "inner join product_ p on op.pid=p.id")
    @Results({
        @Result(property="id", column="id"),
        @Result(property="code", column="code"),
        @Result(property="orderItems", javaType=List.class, column="id", many=@Many(select="com.how2java.mapper.OrderItemMapper.getOrderItemsByOrderId"))
    })
    public List<Order> getOrders();
}
```

#### （8）OrderItem.java

略

#### （9）OrderMapper.java

```java
public interface OrderItemMapper {
    @Select("select * from order_product_ where oid=#{orderId}")
    @Results({
        @Result(property="product", column="pid",    one=@One(select="com.how2java.mapper.ProductMapper.getProductById"))
    })
    public List<OrderItem> getOrderItemsByOrderId(int orderId);
}
```

所谓的 **多对多**，不过是中间多了一个映射关系，检索时，使用 **多对一** 和 **一对多** 组合的方式。

#### （10）TestMybatisByAnnotation.java

```java
InputStream inputStream = Resources.getResourceAsStream(resource);
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
sqlSession = sqlSessionFactory.openSession();

categoryMapper = sqlSession.getMapper(CategoryMapper.class);
productMapper = sqlSession.getMapper(ProductMapper.class);
orderMapper = sqlSession.getMapper(OrderMapper.class);

doSomething();
sqlSession.commit();
sqlSession.close();
```

**说明：**

相比于之前，这里要获取 ***mapper*** 对象来调用接口，调用接口后，即可获取对应的数据结果。

