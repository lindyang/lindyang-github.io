---
title: "T Diagram"
date: 2023-07-28T16:07:22+08:00
tags: [cl]
---


![image.png](https://upload-images.jianshu.io/upload_images/14001410-18bf3924a870351f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Cpython
```
Python    字节码
        C
```
as
```
C    汇编
   B
```

可以产生 Python 的`新型`编译器
```
Python    字节码
      汇编
```
![image.png](https://upload-images.jianshu.io/upload_images/14001410-f9c3acf97e63ac3c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
rust   riscv-elf
   c-rustc  | c    x86-elf
                x86-gcc
```
```
rust  riscv-elf
   x86-elf
```

在 x86 平台上用 gcc 编译 c 写的目标机器为 riscv 的 rustc 项目，得到的执行文件，可以直接在 x86 平台上将 rust 代码交叉编译为 riscv 平台上运行的程序。

```
We use
the symbol ε (epsilon) to denote the empty string,
and we define the metasymbol ε
(boldface ε) by setting L(ε) = {ε}.

whose language is the empty set, which we
write as {}.
We use the symbol Φ for this, and we write L(Φ) = {}.

Note the difference
between {} and {ε}:
the set {} contains no strings at all,
while the set {ε} contains the
single string consisting of no characters.
```

```
r|s is the union of the languages of r and s,
or L{r|s) = L{r) U L(s) = {r, s}
```
```
S1 = {aa,b} and S2 = {a, bb],
then S1S2 = {aaa, aabb, ba, bbb}.

L(r1r2 . . . rn) = L(r1)L(r2) . . . L{rn) =
the set of strings formed by concatenating all
strings from each of L(r1),..., L(rn).
```
- Kleene closure: r*
![image.png](https://upload-images.jianshu.io/upload_images/14001410-2044410382f84cb6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
L(r*) = L(r)*
- 数学运算 * 比 + 优先级高
- `*` is given the highest precedence,
`concatenation` is given the next highest, and `|` is given the lowest
![image.png](https://upload-images.jianshu.io/upload_images/14001410-216c483adef5066d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/14001410-253bf50636275c52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

(r*|s*)* = (r|s)*
[^abc]
```
any character that is not a or b or c
```
- ~(ab)
```
b*(a*~(a|b)b*)*a*
```
![image.png](https://upload-images.jianshu.io/upload_images/14001410-cc7878bc986b8b58.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](https://upload-images.jianshu.io/upload_images/14001410-8e6a0d60c16c8ad3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


