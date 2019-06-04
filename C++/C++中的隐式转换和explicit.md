## 隐式转换

c++中的数据类型转换分为隐式转换和显示转换；

- 显示转换即使用static_cast等方法进行转换，相关内容请参考 **《C++数据类型转换》**；
- 隐式转换则是编译器完成的，如，bool和 int 之间的默认转换；
- 实际开发中，应尽量避免使用隐式转换，代码是给人看的，不是用来炫技的；

#### 类构造中的隐式转换

隐式转换有时候很方便，但是有时候却会产生不易察觉的错误。下面以类构造函数中的隐式转换为例：

```c++
//test.h
class Test
{
public:
	Test(int value){
        this->value = value;
    }
	int GetValue() const{
        return this->value;
    }
private:
	int value;
};
```

```c++
//main.cpp
void Function(const Test& test){
    cout<<test.GetValue();
}

void main(){
    Test test(123);
    Function(test);
}
```

- 如上，代码很简单，Test在构造时需要传入一个 int 型的参数；

- Function()函数需要传入一个 const 的 Test 对象作为函数参数；

但是，若main()函数出现下面的代码，则会如何？

```c++
void main(){
    Function(123);
}
```

事实是，这样也会正常运行，其实不难理解，这就是利用了类构造的隐式转换完成的。

- Function() 方法在接收到参数123后，使用Test的构造函数构造了一个临时的Test对象；
- 因为对象是临时且无法更改的，所以，这里的函数参数需要 const，拿掉则编译错误；

这里的代码看起来非常巧妙，但是却是真正意义上的“奇技淫巧”，老老实实构造一个对象，不好吗？

那么，这里使用隐式转换有什么缺点呢？

最大的问题就是维护，这里示例的代码量少，尚且觉得巧妙，对于中型及其以上的的工程来说，这就是维护的噩梦。

## 使用explicit来禁用构造函数的隐式转换

上例中的构造函数中的隐式转换可以使用 **explicit** 来禁用

```c++
//test.h
class Test
{
public:
	explicit Test(int value){
        this->value = value;
    }
	int GetValue() const{
        return this->value;
    }
private:
	int value;
};
```

```c++
//main.cpp
void Function(const Test& test){
    cout<<test.GetValue();
}

void main(){
    Function(123);	//编译到这里就会报错
}
```

在实际开发中，应尽量避免使用隐式转换，越是看似巧妙，越要避免。