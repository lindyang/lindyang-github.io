---
title: "Three Ways Using Ui"
date: 2023-09-01T10:21:15+08:00
tags: [qt]
---

# ui  文件使用的3种方式

### 导读

- uic 转换为 c++ 代码
- QUiLoader 解析 XML，动态生成 widget 树
- setupUI()
- retranslateUi()

### 三种使用方式

#### 方式1：直接附加

```
// main.cpp
#include "ui_widget.h"

int main() {
	...
    QWidget *w = new QWidget;
    Ui::Widget ui;
    ui.setupUi(w);

    w.show();
    ...
}
```



#### 方式2：单继承

##### 成员变量

```
// .h
#include "ui_widget.h"

class Widget: public QWidget {
	...
private:
	Ui::Widget ui;
}

// .cpp
Widget::Widget(QWidget *parent): QWidget(parent) {
	ui.setupUi(this);
}
```

##### 成员变量指针(推荐， QtCreator 生成的 ui 默认采用这种方式，ui 更改减少编译)

```
// .h
namespace Ui {
class Widget;
}

class Widget: public QWidget {
	...
private:
	Ui::Widget *ui;
}

// .cpp
#include "ui_widget.h"

Widget::Widget(QWidget *parent): QWidget(parent), ui(new Ui::Widget) {
	ui->setupUi(this);
}
```

#### 方式3： 多继承 (不需要 ui 前缀去访问)

```
// .h

#include "ui_widget.h"

class Widget: public QWidget, private Ui::Widget {  // 注意这里的私有继承
	...
}

// .cpp
Widget::Widget(QWidget *parent): QWidget(parent) {
	setupUi(this);
}
```
