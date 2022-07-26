# Qt点击按钮进行页面的切换

## **widget.h**

```c++
#ifndef WIDGET_H
#define WIDGET_H

#include <QWidget>

class Widget : public QWidget
{
	Q_OBJECT

public:
	Widget(QWidget *parent = 0);
	~Widget();

	public slots:
};

#endif // WIDGET_H
```



## widget.cpp

```c++
#include "widget.h"
#include "subwidget.h"

#include <QPushButton>

Widget::Widget(QWidget *parent) : QWidget(parent)

{

	this->resize(800,600);

	this->setWindowTitle("登录");

	//定义一个查询窗口
	subwidget *s = new subwidget();

	//创建一个下一页的按钮
	QPushButton *btn = new QPushButton("下一页",this);

	connect(btn,&QPushButton::clicked,[=](){

	//当前窗口隐藏
	this->hide();

	//查询窗口显示
	s->show();

	});

	//监测查询窗口s的回退信号
	connect(s,&subwidget::back,[=](){

	//隐藏查询窗口
	s->hide();

	//显示当前窗口
	this->show();

	});

}

Widget::~Widget()
{

}
```



## **nextwidget.h**

```c++
#ifndef SUBWIDGET_H
#define SUBWIDGET_H
#include <QWidget>

class subwidget : public QWidget
{
	Q_OBJECT

public:

	explicit subwidget(QWidget \*parent = nullptr);

	signals:

	void back();

	public slots:

};

#endif // SUBWIDGET_H


```



## **nextwidget.cpp**

```c++
#include "subwidget.h"
#include <QPushButton>

subwidget::subwidget(QWidget *parent) : QWidget(parent)
{
	this->resize(800,600);

	this->setWindowTitle("查询");

	//定义一个回退按钮
	QPushButton *btn = new QPushButton("back",this);

	//当按下back 就发出一个back信号
	connect(btn,&QPushButton::clicked,[=](){

	emit this->back();

	});

}
```


