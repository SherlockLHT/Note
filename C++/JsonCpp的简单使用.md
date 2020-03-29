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

头文件在 include/json 目录下，cpp在 src/lib_json 目录下

使用时，将json文件夹添加到项目目录下，将lib_json文件下的所有文件添加到项目目录下，编译的时候相关cpp会提示找不到头文件，但是头文件就在目录下，因为使用的是<>所以找不到，把当前项目路径添加到项目依赖路径下即可

```cmake
include_directories(${PROJECT_SOURCE_DIR})
```

添加进项目项目即可

# 代码示例

## Json对象转换成字符串

### 1、序列化

```c++
Json::Value root;
Json::Value data;

root["errcode"] = 0;        //key添加整型value
data["author"] = "sherlock";//key添加字符串的value
root["data"] = data;        //key添加json类型的value

Json::String jsonStr = root.toStyledString();//将JSON对象序列化为字符串，有缩进
```

### 2、流

```c++
Json::StreamWriterBuilder builder;
Json::StreamWriter* writer = builder.newStreamWriter();
writer->write(root, &cout);

ofstream stream;
stream.open("test.json");
writer->write(root, &stream);	//这里第二个参数传入需要写入的流指针
stream.close();
delete writer;
```

### 3、字符串

jsoncpp新版有所更改，旧方法可以使用，但是编译会有警告

**（1）旧版**

```c++
Json::FastWriter writer;
std::string jsonFile = writer.write(root);	//此格式没有缩进
```

**（2）新版方法**

```c++
Json::StreamWriterBuilder builder;
std::string jsonStr = Json::writeString(builder, root);
```

## Json对象的构建和解析

### 1、从流中获取

```c++
std::ifstream stream;
stream.open("test.json");

Json::Value root;
Json::CharReaderBuilder builder;
builder["collectComments"] = true;

JSONCPP_STRING error;
if(!Json::parseFromStream(builder, stream, &root, &error))
{
	cout<<error<<endl;
}
stream.close();
```

### 2、通过字符串构建

**旧版：**

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

**新版：**

```c++
Json::CharReaderBuilder builder;
Json::CharReader* reader = builder.newCharReader();

Json::Value root;
JSONCPP_STRING error;
if(!reader->parse(jsonStr.c_str(), jsonStr.c_str() + jsonStr.length(), &root, &error))
{
	cout<<error<<endl;
}
delete reader;
```



