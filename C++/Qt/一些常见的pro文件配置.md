```python
UI_DIR = ./ui	#ui文件目录
TARGET = Test	#最终生成目标名
DESTDIR = $$PWD/../test	#目标生成目录，$$PWD表示当前目录下
DLLDESTDIR = $$PWD/../bin	#dll目录，若生成dll则会将dll copy一份到此目录下
OBJECTS_DIR = $$PWD/../obj	#对象文件的存放路径
MOC_DIR = $$PWD/../moc		#moc文件的存放路径

CONFIG(debug, debug|release){
    #debug时的配置
} else {
    #release时的配置
}
```

