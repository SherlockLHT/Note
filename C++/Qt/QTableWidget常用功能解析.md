[TOC]

参考：https://www.cnblogs.com/mfmdaoyou/p/6851703.html

## 说明

***QTableWidget*** 是Qt中常用的表格控件，这里只是做一些简单的功能备注，有需要的时候方便查看

## （1）设置表单样式

```c++
table_widget->setColumnCount(4); //设置列数
table_widget->horizontalHeader()->setDefaultSectionSize(150); 
table_widget->horizontalHeader()->setClickable(false); //设置表头不可点击（默认点击后进行排序）

//设置表头内容
QStringList header;
header<<tr("name")<<tr("last modify time")<<tr("type")<<tr("size");
table_widget->setHorizontalHeaderLabels(header);

//设置表头字体加粗
QFont font = this->horizontalHeader()->font();
font.setBold(true);
table_widget->horizontalHeader()->setFont(font);

table_widget->horizontalHeader()->setStretchLastSection(true); //设置充满表宽度
table_widget->verticalHeader()->setResizeMode(QHeaderView::ResizeToContents);
table_widget->verticalHeader()->setDefaultSectionSize(10); //设置行高
table_widget->setFrameShape(QFrame::NoFrame); //设置无边框
table_widget->setShowGrid(false); //设置不显示格子线
table_widget->verticalHeader()->setVisible(false); //设置垂直头不可见
table_widget->setSelectionMode(QAbstractItemView::ExtendedSelection);  //可多选（Ctrl、Shift、  Ctrl+A都能够）
table_widget->setSelectionBehavior(QAbstractItemView::SelectRows);  //设置选择行为时每次选择一行
table_widget->setColumnWidth(0, 50);//设置某一列宽度
table_widget->setColumnHidden(0, true);//设置某一列是否隐藏
table_widget->horizontalHeader()->setStretchLastSection(true);//最后一列填满
table_widget->setEditTriggers(QAbstractItemView::NoEditTriggers); //设置不可编辑
table_widget->horizontalHeader()->resizeSection(0,150); //设置表头第一列的宽度为150
table_widget->horizontalHeader()->setFixedHeight(25); //设置表头的高度
table_widget->setStyleSheet("selection-background-color:lightblue;"); //设置选中背景色
table_widget->horizontalHeader()->setStyleSheet("QHeaderView::section{background:skyblue;}"); //设置表头背景色

//设置水平、垂直滚动栏样式
table_widget->horizontalScrollBar()->setStyleSheet("QScrollBar{background:transparent; height:10px;}QScrollBar::handle{background:lightgray; border:2px solid transparent; border-radius:5px;}QScrollBar::handle:hover{background:gray;}QScrollBar::sub-line{background:transparent;}QScrollBar::add-line{background:transparent;}");

table_widget->verticalScrollBar()->setStyleSheet("QScrollBar{background:transparent; width: 10px;}QScrollBar::handle{background:lightgray; border:2px solid transparent; border-radius:5px;}QScrollBar::handle:hover{background:gray;}QScrollBar::sub-line{background:transparent;}QScrollBar::add-line{background:transparent;}");
```

### 问题1：鼠标点击的选项出现虚框

```c++
#include "no_focus_delegate.h"
void NoFocusDelegate::paint(QPainter* painter, const QStyleOptionViewItem & option, const QModelIndex &index) const
{
	QStyleOptionViewItem itemOption(option);
	if (itemOption.state & QStyle::State_HasFocus)
	{
		itemOption.state = itemOption.state ^ QStyle::State_HasFocus;
	}
	QStyledItemDelegate::paint(painter, itemOption, index);
}
```

```c++
table_widget->setItemDelegate(new NoFocusDelegate());
```

实现一个类，设置进去即可去除虚框

### 问题2：当表格只有一行的时候，会出现表头塌陷的问题

```c++
//点击表时不正确表头行光亮（获取焦点） 
table_widget->horizontalHeader()->setHighlightSections(false);
```

## （2）表格操作

### 1. 动态插入行

```c++
int row_count = table_widget->rowCount(); //获取表单行数
table_widget->insertRow(row_count); //插入新行
QTableWidgetItem *item = new QTableWidgetItem();
QTableWidgetItem *item1 = new QTableWidgetItem();
//设置相应的图标、文件名、最后更新时间、相应的类型、文件大小
item->setIcon(icon); //icon为调用系统的图标。以后缀来区分   
item->setText(name);
item1->setText(last_modify_time);
item2->setText(type); //type为调用系统的类型。以后缀来区分
item3->setText(size);
table_widget->setItem(row_count, 0, item);
table_widget->setItem(row_count, 1, item1);    
//设置样式为灰色
QColor color("gray");
item1->setTextColor(color);
```

### 2. 指定位置插入行

```c++
table_widget->insertRow(row); //插入新行 row为插入的位置
```

## （3）点击表头触发事件

```c++
connect(horizontalHeader(),SIGNAL(sectionClicked(int)),this,SLOT(onHeaderClicked(int)));
```

## （4）获取选中内容以及行号

**法一：**

```c++
// QTableWidget选中全部单元格及取消选中全部单元格
tableWidget->selectAll();
tableWidget->setFocus();

int rowCount = ui->TableWidget->rowCount();
qDebug()<<"rowcount"<<rowCount;

int colCount = ui->TableWidget->columnCount();
qDebug()<<"colcount"<<colCount;

QTableWidgetSelectionRangerange(0,0,rowCount-1,colCount-1);
tableWidget->setRangeSelected(range,true);//false不选中
tableWidget->setFocus();

QList<QTableWidgetItem*> items = tableWidget->selectedItems();
int count = items.count();
for(int i = 0; i < count; i++)
{
	int row = tableWidget->row(items.at(i));//获取选中的行
	QTableWidgetItem* item = items.at(i);
	QString name = item->text();//获取内容
}
```

**法二：**

```c++
QList<QTableWidgetSelectionRange> ranges = tableWidget->selectedRanges();
int count = ranges.count();
for(int i = 0; i < count; i++)
{
	int topRow = ranges.at(i).topRow();
	int bottomRow = ranges.at(i).bottomRow();
	for(int j = topRow; j <= bottomRow; j++)
	{
		qDebug()<<"selectRow"<<j;
	}
}
```

**法三：**

```c++
QModelIndexList selected = ui->tableView->selectionModel()->selectedRows();
int index = selected.first().row();//一行
```

## （5）合并单元格

```c++
tableWidget->setSpan(0, 0, 3, 1) //其參数为： 要改变单元格的 1行数 2列数 要合并的 3行数 4列数
```

