# 配置文件

SpringBoot有一个全局的配置文件，名称固定：

- application.properties
- application.yml

**作用：**修改自动配置的默认值

# YAML语法

## 基本语法

- **key:空格value：**表一一对数据，空格不能省略
- 用缩进表示层级关系；
- 大小写敏感

```yaml
server:
	port: 8081
	path: /hello
```

- 上例中，port和8081中间有一个空格，表示一对数据
- port和path是同一个层级的数据，

## value类型

### （1）普通值

**不加引号**：普通的值

**双引号：**转义字符正常转义，如\n将会编程换行

**单引号：**转义字符不转义，\n就还是\n

```yaml
port: 8081
```

### （2）对象、Map

```yaml
server:
	port: 8081
	path: /hello
```

```yaml
server: {port: 8081, path: /hello}
```

上面两种写法效果相同，注意空格

### （3）数组（set/list）

```yaml
categories:
 - c++
 - Java
```

```yaml
categories: [c++, Java]
```

上面两种写法效果相同，注意空格