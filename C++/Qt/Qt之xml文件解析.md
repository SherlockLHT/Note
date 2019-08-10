[TOC]

## XML文件简介

- XML - **EX**tensible **M**arkup **L**anguage，可拓展标记语言

## Qt中加载XML模块

**.pro** 文件中添加

```python
QT += xml
```

## Qt的XML访问方式
引用：https://blog.csdn.net/liang19890820/article/details/52805902
> Qt 提供了两种访问 XML 文档的方式：DOM 和 SAX。
>
> - DOM 方式：将 XML 文档转换为树形结构存储到内存中，再进行读取，消耗的内存比较多。此外，由于文档都已经存储到内存，所以需要频繁实现修改等操作时，使用起来比较方便。
>
> - SAX 方式：相比于 DOM，SAX 是一种速度更快，更有效的方法，它逐行扫描文档，一边扫描一边解析（由于应用程序只是在读取数据时检查数据，因此不需要将数据存储在内存中，这对于大型文档的解析是个巨大优势）。而且相比于 DOM，SAX 可以在解析文档的任意时刻停止解析。但操作复杂，很难修改 XML 数据。

### DOM

| 类  | 描述     |
| ---- | ---- |
|  QDomAttr    |  表示一个 QDomElement 的属性    |
| QDomCDATASection | 表示一个 XML CDATA 部分 |
| QDomCharacterData | 表示 DOM 中的一个通用字符串 |
| QDomComment | 表示一个 XML 注释 |
| **QDomDocument** | 表示一个 XML 文档 |
| QDomDocumentFragment | QDomNodes 树，通常不是一个完整的 QDomDocument |
| QDomDocumentType | 表示文档树中的 DTD |
| **QDomElement** | 表示 DOM 树中的一个元素 |
| QDomEntity | 代表一个 XML 实体 |
| QDomEntityReference | 代表一个 XML 实体引用 |
| QDomImplementation | DOM 实现的功能的信息 |
| QDomNamedNodeMap | 包含一个节点集合，节点可以通过名字来访问 |
| **QDomNode** | 一个 DOM 树中所有节点的基类 |
| QDomNodeList | QDomNode 对象列表 |
| QDomNotation | 代表一个 XML 表示法 |
| QDomProcessingInstruction | 代表 XML 处理指令 |
| QDomText | 表示解析的 XML 文档中的文本数据 |

**说明：**

- XML的每级元素（QDomElement），也可以称之为结点（QDomNode），QDomElement继承自QDomNode；
- QDomNode可以使用`toElement()`方法转换成QDomElement；

#### 常用方法

````c++
QDomDocument doc("test_xml");
QFile xml_file("FiltersConf.xml");
if(!doc.setContent(&xml_file))	//也可以传入字符串
{
	qDebug()<<"set content fail";
	return 0;
}
QDomElement root_element = doc.documentElement();//获取xml文件的根元素
qDebug()<<root_element.tagName();	//使用tagName()方法获取元素的标签名
QDomNode node = root_element.firstChild();//获取第一个子结点
QString attr = node.toElement().attribute("name");//获取属性
while(!node.isNull())
{
	qDebug()<<"-"<<node.toElement().tagName();
	node = node.nextSibling();	//获取同级的结点
}
````

## 写入XML

```c++
QDomDocument document;
//xml头部的<?xml version="1.0" encoding="UTF-8"?>
QDomProcessingInstruction instruction = document.createProcessingInstruction("xml", "version=\"1.0\" encoding=\"UTF-8\"");
document.appendChild(instruction);
QDomElement root_node = document.createElement("transpond");//创建根结点
document.appendChild(root_node);    //添加根结点

QDomElement element = document.createElement("machine");//创建元素结点
element.setAttribute("type", "machine");
root_node.appendChild(element);//元素结点添加到根结点下

QDomElement item_element = document.createElement("machine-item");//创建item结点
item_element.setAttribute("type", "11");
item_element.setAttribute("name", "22");
item_element.setAttribute("device-code", "33");
item_element.setAttribute("ip", "44");
item_element.setAttribute("sync-time", "55");

//写入文件
QFile file(pConfigManager->GetMachineInfoFile());
if(!file.open(QIODevice::ReadWrite | QIODevice::Truncate))
{
	return false;
}
QTextStream in(&file);
document.save(in, 4);
file.close();
```

