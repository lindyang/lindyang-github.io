---
title: "Wine"
date: 2023-07-28T14:55:29+08:00
tags: [misc]
---


export WINEARCH=win<32|64>
export WINEPREFIX=$HOME/.wine

http://mirrors.ustc.edu.cn/wine/wine/
下载 wine-mono 和 wine-gecko 两个插件

```
wine start /i wine-mono-5.0.0-x86.msi
wine start /i wine-gecko-2.47.1-x86.msi
wine start /i wine-gecko-2.47.1-x86_64.msi
```
