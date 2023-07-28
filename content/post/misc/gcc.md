---
title: "Gcc"
date: 2023-07-28T14:53:46+08:00
tags: [misc]
---


### 选项

- L(大写l): 库所在位置
> -Ldir
           Add directory dir to the list of directories to be searched for -l.
- l(小写l): 库
> -llibrary
> -l library  // POSIX compliance, not recommended.
> 除非指定 -static, 否则优先共享库;
> 顺序很重要;
> 循环引用要多次指定(gcc foo.c -lfoo -lbar -lfoo)
> -( -) -start-group和--end-group 指定循环依赖关系的归档

- I(大写i): include


### 变量
- C_INCLUDE_PATH: 在 `I` 选项之后，在默认目录之前被搜索。
```
/usr/local/include/
/usr/include/
```
- LIBRARY_PATH: gcc `编译`时动态链接库
- LD_LIBRARY_PATH: 程序`加载运行`时动态链接库
> 在 `L` 选项之后，在默认目录之前被搜索。
```
/usr/local/lib/
/usr/lib/
```

```
-fno-builtin
-fno-builtin-function: -fno-builtin-printf
ld -static
gcc -shared -fPIC -o lib.so lib.c
```

### gcc -m32
### ld -static -m elf_i386
### pip
```
pip install \
    --global-option=build_ext \
    --global-option="-I/tmp/openssl/include" \
    --global-option="-L/tmp/openssl/lib" \
    --global-option="-R/tmp/openssl/lib" \
    cryptography==1.0;
```

### CFLAGS
```
CFLAGS=-I/usr/include -I/path/include
```
### LDFLAGS和LIBS区别
```
LDFLAGS = -L/var/xxx/lib -L/opt/mysql/lib -Wl,R/var/xxx/lib -Wl,R/opt/mysql/lib
LIBS = -lpthread -liconv
```

### [ld](https://biscuitos.github.io/tags#Linker)
### [ld](https://github.com/iDalink/ld-linker-script)


### python 调用 so
```
import ctypes
from cytpes import create_string_buffer, c_int, c_char_p, c_void_p
so = ctypes.cdll.LoadLibrary
lib = so("./lib.so")
p = create_string_buffer(512)
code = lib.enums(p,sizeof(p), 0);

def callback_fn(buf, void_p):
    ...
    return 0
# end def

void_ppp = 123456
CMPFUNC = ctypes.CFUNCTYPE(c_int, c_char_p, c_void_p)
_callback_fn = CMPFUNC(callback_fn)
code = lib.monitor(_callback_fn, void_ppp)
```

### clang --target=i386
```
gcc ftrace.c -o ftrace
clang -o i64 main.c
./ftrace i64
```
