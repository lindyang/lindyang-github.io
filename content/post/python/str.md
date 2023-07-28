---
title: "Str"
date: 2023-07-28T15:52:36+08:00
tags: [python]
---


#### PY3 对应原则
- wb 仅对应 `字节` 写
- w 仅对应 `字符串` 写

#### PY2
并没有像 PY3 一样遵守`对应原则`。
```
open('/tmp/_', 'wb').write(u'汉字')
```
 也是可以的。可能的报错
> UnicodeEncodeError: 'ascii' codec can't encode characters...

只是指明默认的 ascii 规则不能 encode `汉字`，此时需要指定默认编码
```
import sys
try:
    reload(sys)
    sys.setdefaultencoding('utf-8')
except NameError:
    pass
```

#### PY2、PY3 字符串的区别
| 变量 | 'a' | u'a' | b'a'
|------|------|--------|--------|
PY2  | <type 'str'> | <type 'unicode'> | <type 'str'>
PY3  | <class 'str'> | <class 'str'> | <class 'bytes'>

- 所以编写 PY2、PY3 兼容的代码时，对于非 ASCII 的字符，需要加 `u`(e.g `u'汉字'`)。
- 但是有时，对于 ASCII 字符，需要加 `b`，明确指明其是一个 bytes:
> pipe = Popen(["ls"], stdout=PIPE).stdout
> for line in iter(pipe.readline, `b''`)


