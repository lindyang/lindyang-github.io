---
title: "Core Dump"
date: 2023-07-28T14:27:47+08:00
tags: [bash]
---


```
修改 /etc/sysctl.conf
加入 kernel.core_pattern = core
然后 sysctl -p 使其生效
ulimit -c unlimited 设置核心转储文件的大小
```

