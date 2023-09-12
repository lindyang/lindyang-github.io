---
title: "Qt Ftp"
date: 2023-09-12T18:17:56+08:00
tags: [qt]
---


### 下载源码
```
http://github.com/qt/qtftp
```

### 打开顶层项目, 修改 qtftp.pro

```
# 注释掉 module_qtftp_examples 和 module_qtftp_tests
TEMPLATE = subdirs
...
SUBDIRS += module_qtftp_src
# \
#           module_qtftp_examples \
#           module_qtftp_tests \
```

### 修改 src\qftp\qftp.h
```
//#include <QtFtp/qurlinfo.h>
#include "qurlinfo.h"
```

### 修改 src\qftp\qftp.pro 进行动态编译
```
CONFIG -= staticlib
CONFIG += shared
```

### 复制动态 lib\
- Qt5Ftp.dll
- Qt5Ftpd.dll
- Qt5Ftpd.pdb

到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\bin


### 修改 src\qftp\qftp.pro 进行静态编译
```
CONFIG += staticlib
CONFIG -= shared
```

### 拷贝 lib\cmake\Qt5Ftp 
目录到到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\lib\cmake


### 复制静态 lib\
- Qt5Ftp.lib
- Qt5Ftpd.lib
- Qt5Ftp.prl
- Qt5Ftpd.prl

到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\lib


### 将 mkspecs\modules-inst\qt_lib_ftp.pri 

拷贝到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\mkspecs\modules


### 在编译输出目录 include\QtFtp 做以下操作
- 创建 QFtp 文件, 仅包含内容 `#include "qftp.h"`
- 恢复源码 src\qftp\qftp.h, 并拷贝到此处
- 拷贝 src\qftp\qurlinfo.h 到此处

### 将上面的目录 include\QtFtp 
拷贝到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\include

