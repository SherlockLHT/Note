## 说明

**mybatis** 在 **xml** 文件中配置 **sql** 时有时要传入参数，单个参数使用 **parameterType** 指定参数类型即可，多参数呢？

## 多参数传递方法

### （1）使用下标

- 不写 **parameterType**；

```java
public List<Product> getProductList(int id, String name, float price);
```

```xml
<select id="getProductList" result="Product">
  select * from product_ where id=#{0} and name=#{1} and price=#{2}
</select>
```

### （2）使用注解

- 不写 **parameterType**；

```java
public List<Product> getProductList(@Param("id")int id, @Param("name")String name);
```

```xml
<select id="getProductList" result="Product">
  select * from product_ where id=#{id} and name=#{name}
</select>
```

### （3）使用map

- **map** 中包含使用到的参数；

```java
public List<Product> getProductList(HashMap map);
```

```xml
<select id="getProductList" parameterType="hashmap" result="Product">
  select * from product_ where id=#{id} and name=#{name}
</select>
```

