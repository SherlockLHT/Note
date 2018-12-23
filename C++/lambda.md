## lambda各部分

```c++
[=] (int value) mutable throw() -> int { doSomeThing; }
```

![](E:\Note\C++\lambda.png)

1. *capture*子句，又称*lambda-introducer(lambda引出符)*，默认值传递;
2. 参数列表，可选，和普通函数无异，参数列表为空，则可以省略；
3. *mutable*可变规范，*lambda*函数总是一个*const*函数，*mutable*可以取消常量属性（使用时，参数列表即使为空，也不能省略）；
4. *exception-specification*可选；
5. 返回值类型，可选，默认*auto*;
6. *lambda*函数主体；

## 捕获

- 捕获`this`表示可以使用成员变量；

**语法如下：**

```c++
//默认值传递
//若不指定某个变量，则默认指全部变量
[param]值传递捕获变量param
[=]值传递捕获父作用于内所有变量，包括this
[&]应用传递
```

示例：

```c++
[&, i]{}	//OK，值传递捕获i，其余的应用传递
[&, &i]{}	//error，应用传递捕获所有变量，&i重复
[=, this]{}	//c++20前，error，值传递捕获所有变量，this重复
			//c++20后，OK，都是值捕获
[=, *this]{}	//c++17前，error无效语法
				//C++17 OK，通过封闭捕获类
```

## 代码示例

```c++
qDebug()<<[](double num){ return std::abs(num); } (-5.4);

qDebug()<<[](double num) -> int { return std::abs(num); }(-5.4);

auto lambda = [](double num)->int{return std::abs(num);}(-5.5);
qDebug()<<lambda;

auto temp = [](double num)->double{return std::abs(num);};

qDebug()<<temp(-5.6);

qDebug()<<[]{return "No Param.";}();

int m = 0;
int n = 0;
[&, n] (int a) mutable { m = ++n + a; }(4);
qDebug()<<m<<","<<n;//5,0
```

