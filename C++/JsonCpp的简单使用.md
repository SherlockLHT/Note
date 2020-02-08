# 说明

JsonCpp是最常用的C++解析的Json库，跨平台，开源

本文只介绍JsonCpp的一些简单入门的用法，深入学习请自行研究

下载地址：https://github.com/open-source-parsers/jsoncpp

# 使用方法：

**方法一：**

编译成链接库调用。

编译 src/lib_json目录下的代码，得到 libjsoncpp.a，加入到项目中使用

**方法二：**

直接使用代码。

头文件在 include/json 目录下，cpp在 src/lib_json 目录下，添加进项目项目即可

# 代码说明：

JsonCpp 主要包含三种类型的类：**Value、Reader、Writer**

JsonCpp的所有数据结构都位于 namespace Json中，在文件 json.h 内，使用时，只需要include json.h 即可

Json::Value 类用于 JSON 数据类型的保存，可以获取某个指定key，并转换成各种数据类型

Json::Reader 类用于解析JSON数据

Json::Writer 类用于生成JSON数据

详细的使用方法参考源码目录下的doc文件，使用doxygen工具可以生成doxygen页面

下面代码示例只是简单的使用示例

## 1、解析JSON示例：

```c++
Json::Reader reader;
Json::Value root;
if(!reader.parse(jsonStr, root))	//解析JSON字符串
{
	cout<<"parse json str fail"<<endl;
	return;
}
cout<<"parse json str pass"<<endl;
if(!root["errcode"].isNull())
{
	int errorCode = root["errcode"].asInt();
	cout<<"error code is "<<errorCode<<endl;
}
if(root["data"].isNull())
{
	cout<<"no data"<<endl;
	return;
}
Json::Value data = root["data"];
//Json::String对象是stl的string的别名
Json::String author = data["author"].asString();
```

- 可以使用 **[]** 来获取key的值，失败则创建空的Value；
- 也可以使用 **get()** 方法获取key的值，可以设置默认值；

## 2、构建JSON示例：

```c++
Json::Value root;
Json::Value data;

root["errcode"] = 0;        //key添加整型value
data["author"] = "sherlock";//key添加字符串的value
root["data"] = data;        //key添加json类型的value

Json::String jsonStr = root.toStyledString();//将JSON对象序列化为字符串
cout<<jsonStr<<endl;
```

输出结果：

```c++
{
	"data" :
	{
		"author" : "sherlock"
	},
	"errcode" : 0
}
```

## 3、在数据中插入JSON对象

将2中的 Json::Value 对象输入到数据流中

```c++
Json::StreamWriterBuilder builder;
Json::StreamWriter* writer = builder.newStreamWriter();
writer->write(root, &cout);	//输入到输出流中
cout<<endl;	//换行并刷新
```

将2中的对象输入到文件流中

```c++
Json::StreamWriterBuilder builder;
Json::StreamWriter* writer = builder.newStreamWriter();
ofstream stream;
stream.open("test.json");
writer->write(root, &stream);
stream.close();
```





