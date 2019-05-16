```python
UI_DIR = ./ui	#ui文件目录
TARGET = Test	#最终生成目标名
DESTDIR = $$PWD/../test		#目标生成目录，$$PWD表示当前目录下
DLLDESTDIR = $$PWD/../bin	#dll目录，若生成dll则会将dll copy一份到此目录下
OBJECTS_DIR = $$PWD/../obj	#对象文件的存放路径
MOC_DIR = $$PWD/../moc		#moc文件的存放路径

CONFIG(debug, debug|release){
    #debug时的配置
} else {
    #release时的配置
}

CONFIG   += c++11 (Qt5)	#支持C++11

HEADERS += test.h	#添加头文件
SOURCES += test.cpp	#添加cpp文件

INCLUDEPATH += $$PWD/../qwt			##添加库包含目录
LIBS += -L$$PWD/../qwt/lib -lqwt	#添加库，-L后面是目录，-l后面是库文件
DEPENDPATH += $$PWD/../qwt/lib 		#依赖关系目录

include(../../../Util/util.pri)	#一般用于添加pri文件，添加后，会在项目列表中出现包含的文件列表，没有这个，但是有INCLUDEPATH添加目录，也能构建，但是项目树中不会出现库列表
```

