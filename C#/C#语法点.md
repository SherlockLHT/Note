- 强类型语言

## decimal

后续添加

## 内插字符串

- 模板，用户插入/拼接字符串；
- `$"test{变量名}abcdefg"`，{变量名}会使用变量值替换；

**`string`前面有`$`符号，表示可以在字符串中嵌入C#代码(用{}包含)，即用代码生成的值替换这段C#代码，例如：`$({name.ToUpper()})`**

## 运算符重载

- `operator`后跟运算符符号；
- 必须是`static`；
- 返回新的对象；

```c#
public Test{
    public static Test operator+ (Test a, Test b){
        Test t = new Test();
        //doSomething();
        return t;
    }
}
```

