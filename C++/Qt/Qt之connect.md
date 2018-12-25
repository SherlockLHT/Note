## Qt4之connect

#### 基本用法

```c++
connect(ui->toolButton, SIGNAL(clicked()), this, SLOT(OnClickButton()));
disconnect(ui->toolButton, SIGNAL(clicked()), this, SLOT(OnClickButton()));
```

**返回`bool`以表示信号槽连接/断开是否成功**

```c++
Q_CORE_EXPORT const char *qFlagLocation(const char *method);

#define SLOT(a)     qFlagLocation("1"#a QLOCATION)
#define SIGNAL(a)   qFlagLocation("2"#a QLOCATION)
```

`SIGNAL`和`SLOT`实际上是两个宏定义，将函数名及其参数列表转换成一个字符串，随后`moc`会扫面全部文件，将所有的信号和槽提取出来做成一个映射表。

#### 设置回调函数

利用`SIGNAL`和`SLOT`两个宏定义转换字符串的功能，可以利用之设置回调函数

```c++
void SetCallback(const char* callback){
    connect(this, SIGNAL(TestSignal()), this, callback);
}
```

```c++
SetCallback(SLOT(TargetFunction));	//调用
```

## Qt5之connect

- Qt5兼容旧版的信号槽连接机制；
- 新增新语法连接信号槽，使用函数指针；

**示例：**

```c++
QMetaObject::Connection cobject = connect(ui->toolButton, &QToolButton::clicked, this, &Widget::OnClickButton);

disconnect(connect_object);
```

```c++
connect(ui->toolButton, &QToolButton::clicked, TestFunction)
```

```c++
int temp = 123;
connect(ui->toolButton, &QToolButton::clicked, [temp](){ 
    qDebug()<<"Test Function"<<temp; 
});	//lambda
```

#### 优点：

- 编译检查；
- 参数自动隐式转换；
- 不需要指定`public/private slots`；
- 不需要`moc`，任何函数都可以作为槽函数；

#### 缺点：

- 信号不能重载，若有信号重载，则编译错误（信号槽应该避免使用重载）；
- 不支持槽函数的默认参数；