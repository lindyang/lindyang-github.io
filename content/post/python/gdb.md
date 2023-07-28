---
title: "Gdb"
date: 2023-07-28T14:39:43+08:00
tags: [python]
---


1. 创建一个 python-sudo.sh 可执行文件, `作为工程解释器`
```
#!/bin/bash
sudo /path/to/bin/python "$@"
```
2. 将 python 加入 sudo 免密
```
user hostname = (root) NOPASSWD: /path/to/bin/python
```

#### 扩展 gdb
> 如果以 root 用户进行 gdb 调试
```
pkexec /usr/bin/gdb "$@"
```


