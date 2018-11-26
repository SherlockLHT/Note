参考文章：https://www.cnblogs.com/lgxZJ/archive/2017/12/31/8158132.html

## Qt和`JavaScript`的交互

`Qt`提供了对`JS`的良好支持，有两种方式：

- `AScriptEngine`
  - 4.3开始引入，现已被官方抛弃；
- `QJSEngine`
  - 5.0引入；
  - 封装了`V8`引擎；

## Qt中执行脚本

> ```c++
> QJSValue QJSEngine::evaluate(const QString &program, const QString &fileName = QString(), int lineNumber = 1);
> ```

**program**：脚本代码

**fileName/lineNumber**：出错的时候包含在出错信息里

示例：

```javascript
function test(){
	return "123"
}
test();
```

```c++
QFile file("debug/JSTest.js");
if(!file.open(QIODevice::ReadOnly | QIODevice::Text))
{
	return;
}
QString js_code = file.readAll();
file.close();

QJSEngine engine;
QJSValue result = engine.evaluate(js_code);	//执行脚本
QString str_result = result.toString();		//"123"
```

## Qt对脚本的动态控制

Qt中执行脚本，是将脚本代码组成字符串，借此，可以动态控制脚本的代码逻辑

```c++
QString js_code = QString("%1/%2").arg(10).arg(2);
qDebug()<<js_code;	//10/2
QJSValue result = engine.evaluate(js_code);
qDebug()<<result.toString();	//5
```

## 配置`JS`的全局变量

> ```c++
> QJSValue QJSEngine::globalObject() const;
> 
> Returns this engine's Global Object.
> ```
>
> ```c++
> void QJSValue::setProperty(const QString &name, const QJSValue &value);
> 
> Sets the value of this QJSValue's property with the given name to the given value.
> ```

通过`globalObject()`方法获取引擎的全局对象，再使用`setProperty()`方法设置全局属性，该属性可以在`js`脚本中使用。

## Qt的脚本化

> ```c++
> QJSValue QJSEngine::newQObject(QObject *object);
> 
> Creates a JavaScript object that wraps the given QObject object, using JavaScriptOwnership.
> Signals and slots, properties and children of object are available as properties of the created QJSValue.
> ```

使用`newQObject`函数，将Qt类封装成`js`对象，集成在`js`的引擎中。

Qt的信号槽、属性和子对象都可以封装。

将Qt的类封装起来，再通过全局属性将其传给`js`脚本，可以实现`js`和Qt的交互。

示例：

```js
edit.setText("This is test");
```

```c++
QFile file("debug/JSTest.js");
if(!file.open(QIODevice::ReadOnly | QIODevice::Text))
{
	return;
}
QString js_code = file.readAll();
file.close();

QJSEngine engine;
engine.globalObject().setProperty("edit", engine.newQObject(ui->lineEdit));
QJSValue result = engine.evaluate(js_code);	//执行脚本
```

将`ui->lineEdit`控件封装并传给`js`，在脚本中调用，运行后，界面的`lineEdit`控件上会出现`This is test`文字。