## Servlet方法

**Servlet需要提供对应的`doGet()`/`doPost()`方法**

### doGet()

- `form`默认的提交方式
- 指定超链接访问某个地址
- 在地址栏里直接输入某个地址
- `AJAX`指定使用`get`方式

### doPost()

- `form`上指定`method="post"`；
- `AJAX`指定使用`post`方式；

### service()

```java
service(HttpServletRequest, HttpServletResponse);
```

- 在执行`doGet()`和`doPos()`之前都会先执行`service()`；
- `service()`方法进行判断，调用哪一种方法；

## 传递中文参数

**HTML文件中设置utf-8编码**

```html
<meta charset="utf-8" />
```

**将读到的参数进行解码和编码**

```java
byte[] bytes=  name.getBytes("ISO-8859-1");
name = new String(bytes,"UTF-8");
```

**或者直接将请求统一成utf-8编码**

```java
request.setCharacterEncoding("utf-8"); 
```

**同理，返回中文也需要把response统一成utf-8编码**

```java
response.setContentType("text/html; charset=UTF-8");
```

