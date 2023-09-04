---
title: "Qt-Mysql"
date: 2023-09-04T17:15:34+08:00
tags: [qt]
---

### 更改 pro


```
# C:\Qt\Qt5.6.3\5.6.3\Src\qtbase\src\plugins\sqldrivers\mysql

INCLUDEPATH += $$quote(C:\Program Files\MySQL\MySQL Server 5.7\include)

LIBS += -L$$quote(C:\Program Files\MySQL\MySQL Server 5.7\lib) -llibmysql

DESTDIR = $$quote(C:\Users\oem\Desktop\oem)
```

- 将 DESTDIR 生成的 dll 或 lib 复制到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\plugins\sqldrivers
- 将 C:\Program Files\MySQL\MySQL Server 5.7\lib\libmysql.dll(lib) 复制到 C:\Qt\Qt5.6.3\5.6.3\msvc2015\bin



