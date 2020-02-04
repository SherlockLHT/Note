<font size=3>

# 一、说明

QWeb是odoo2使用的主要模板引擎，其实一种XML的模板引擎，主要用来生成HTML片段和页面。

QWeb的模板指令使用特殊的带 **t-** 前缀的XML属性表示

如果要变元素的渲染，可以使用占位符 **\<t\>**，它会执行指令，但是本身不会产生任何输入，示例如下：

```xml
<t t-if="True">
	<p>test</p>
</t>
```

上例中，执行 **\<t>** 内的指令，即判断t-if，并进行内部元素的输出，但是 **\<t>** 本身不会输出

即，输出：**\<p\>test\</p\>**

而，如下写成下面这种形式：

```xml
<div t-if="True">
	<p>test</p>
</div>
```

则，最后输出：

```xml
<div>
	<p>test</p>
</div>
```

# 二、数据输出t-esc

QWeb有一个主要的输出指令 **t-esc**，可以根据提供的表达式求值，并输出。

并且，在展示用户提供的数据的时候，可以自动进行HTML的转义，限制XSS风险

```xml
<p>
	<t t-esc="value"></t>
</p>
```

上例中，**value** 可以是一个表达式，最后会使用最终计算的结果进行呈现

另外还有一个 **t-raw** 指令，作用类似，但是不能进行HTML转义

这里如果要输出字符串 **value**，则加上单引号即可，表示这是一个字符串，而不是变量或者表达式

# 三、条件t-if

qweb可以使用 **t-if、t-elif、t-else** 指令作为条件判断，最后会根据表达式的计算结果进行显示

```xml
<div>
	<t t-if="condition">
        <p>OK</p>
    </t>
</div>
```

```xml
<div>
    <p t-if="user.birthday == today()">Happy birthday!</p>
    <p t-elif="user.login == 'root'">Welcome master!</p>
    <p t-else="">Welcome!</p>
</div>
```

# 四、循环t-foreach/t-as

qweb可以使用 **t-foreach** 指令指定需要迭代的数据，并使用 **t-as** 提供当前项的变量

```xml
<t t-foreach="[1,2,3]" t-as="i">
    <p><t t-esc="i"/></p>
</t>
```

```xml
<p t-foreach="[1, 2, 3]" t-as="i">
    <t t-esc="i"/>
</p>
```

**t-foreach** 可以用于迭代数组、映射表、整形数字（即0~X的数组）

除了 **t-as** 外，还提供了一些其他变量，如下：

| 变量名     | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| $as_all    | 被迭代的对象                                                 |
| $as_value  | 迭代的当前值，如果是列表或者整数，则和 ***$as*** 相同<br>若果是映射，则 ***$as*** 表示key，***$as_value*** 表示value |
| $as_size   | 被迭代的集合的大小                                           |
| $as_first  | 当前是否是第一个元素，等同于 ***$as_index == 0***            |
| $as_last   | 当前是否是最后一个元素，等同于 ***$as_index + 1 == $as_size*** |
| $as_parity | even/odd，表示当前迭代的元素奇偶性                           |
| $as_even   | 当前项目索引是否为奇数                                       |
| $as_odd    | 当前项目索引是否为偶数                                       |

这些变量只能用在循环体内部，且 **$as** 需要用 **t-as** 的名称代替，示例如下：

```xml
<p t-foreach="['a','b','c','d','e']" t-as="data">
    all:  <t t-esc="data_all"></t>
    index:<t t-esc="data_index"></t>
    value:<t t-esc="data_value"></t>
    size: <t t-esc="data_size"></t>
    first:<t t-esc="data_first"></t>
    last: <t t-esc="data_last"></t>
    parity: <t t-esc="data_parity"></t>
    even: <t t-esc="data_even"></t>
    odd:  <t t-esc="data_odd"></t>
</p>
```

# 五、属性

## （1）t-att-*$name*

设置属性，参数是一个表达式，示例：

```xml
<div t-att-a="1+2" />
```

上例渲染的结果为：

```xml
<div a="3"/>
```

## （2）t-attf-*$name*

效果和 **t-att-$name** 相同，但是参数不是表达式，而是一个字符串，可以传递参数；

若参数是变量，则需要用 **{{}}** 包起来

示例：

```xml
<p t-foreach="['a','b']" t-as="data">
	<p t-attf-test="{{data}}"></p>
</p>
```

渲染结果是：

```xml
<p test="a"></p>
<p test="b"></p>
```

## （3）t-att

### 参数是mapping

设置属性，参数是一个key/value映射，示例：

```xml
<p t-att="{'a':'b', 'c':'d'}"></p>
```

渲染结果是：

```xml
<p a="b" c="d"></p>
```

### 参数是pair

设置属性，参数是一对（即数量是2的元素或者数组，超出部分丢弃），第一个元素是属性名，第二个元素是值，示例：

```xml
<p t-att="['a','b','c','d']"></p>
```

渲染结果是：

```xml
<p a="b"></p>
```

# 六、设置变量t-set/t-value

qweb中支持设置变量，使用 **t-set** 指定变量名，使用 **t-value** 指定变量值

```xml
<p>
    <t t-set="param" t-value="9+1" />
    <t t-esc="param" />
</p>
```

渲染结果为：

```xml
10
```

如果没有使用 **t-value** 设置变量值，则默认使用该节点的内容作为变量值，示例：

```xml
<p>
	<t t-set="param">abcdef123</t>
	<t t-esc="param" />
</p>
```

渲染结果为：

```xml
abcdef123
```

同样的，**t-value** 传的是表达式，如果需要赋值字符串，则用单引号即可

# 七、调用子模板t-call

QWeb可以再一个模板中调用另一个模板，视同 **t-call** 指定调用的模板的name即可，但是要注意，名称不要重复

示例：

```xml
<!-- template.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<templates id="test_template" xml:space="preserve">
	<t t-name="test_test_test">
		<t t-call="test_test_test2"/>
	</t>
</templates>

```

```xml
<!-- other.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<templates id="test_template" xml:space="preserve">
	<t t-name="test_test_test2">
        <t t-esc="hello" />
	</t>
</templates>
```

如上，template.xml中调用other.xml中的模板，最后渲染的结果为：

```xml
hello
```

## 向子模板传递参数

**t-call** 是将子模板的内容复制到调用的地方，所以，在 使用 **t-call** 之前定义的变量，可以再子模板里面使用，示例：

```xml
<!-- template.xml -->
<p>
	<t t-set="param" t-value="'main'"/>
	<t t-call="test_test_test2"/>
</p>
```

```xml
<!-- other.xml -->
<t t-name="test_test_test2">
	<p>
		<t t-esc="param"/>
	</p>
</t>
```

渲染的结果为：

```xml
<p>main</p>
```

如上例，在 **t-call** 之前，设置了变量parma，值为字符串main，在子模板中，可以使用此变量

# 八、python

## （1）字段格式化t-field



## （2）调试t-debug



# 九、帮助

## 在controller中返回qweb

在controller中，可以通过 **odoo.http.HttpRequest.render()** 方法，使用数据库中的模板创建一个Response并返回

```python
response = http.request.render('template', {
    'context': 123
});
```

## 在视图中使用qweb

使用 **ir.ui.view** 的 **render()** 方法，通过数据库id呈现qweb视图

# 十、JavaScript

## （1）定义模板t-name

- **t-name** 指令只能放在模板文件的顶层；
- 不需要参数，并且需要使用 **\<t\>** 元素；

示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<templates id="test_template" xml:space="preserve">
	<t t-name="test_test_test">
        <!-- 模板内容 -->
	</t>
</templates>

```

## （2）调试

qweb模板中可以进行一些调试

### 1、t-log

计算表达式并使用 console.log 在控制台打印结果，示例：

```xml
<t t-set="param" t-value="123"></t>
<t t-log="param"></t>
```

### 2、t-debug

在模板的渲染期间处罚单步调试断点，示例：

```xml
<t t-debug=""></t>
```

### 3、t-js

在渲染期间执行js代码，需要传入一个变量以获取上下文参数，变量的值会包含在这个参数对象中，示例：

```xml
<t t-set="foo" t-value="42"/>
<t t-js="context">
	console.log("Foo is", context.foo);
</t>
```

# Helpers

core.qweb(core是 **web.core** 模块):一个 **QWeb2.Engine()** 实例，在这个实例中所有模块相关模板文件全部有加载，而且可使用 **_** (underscore方法), **_t** (翻译函数) 和 JSON
 **core.qweb.render** 可以用于渲染基本模块的模板







