---
title: "Using Numbers"
date: 2023-07-28T16:04:25+08:00
tags: [as]
---


- Double-extended floating-point
- integers more than 1 byte are stored in little endian format
- integers stored in big-endian format within the register
- Signed magnitude
> 两种方式表示 0
> 算数运算复杂

[补码](https://www.zhihu.com/question/20159860/answer/71256667)
- The carry value is ignored in signed integer arithmetic
- movzx source, destination
> smaller-sized unsigned => larger-sized unsigned integer(only register)
- movsx
- x/nfu
> - f: c, d, x, s
> - u: b, h, w, g
- mmx register
> 64bit. Mapped to the FPU registers
- movq source, destination
> MMX, SSE, 64-bit memory location
- print/f
- SIMD
> Single Instruction Multiple Data
- MMX
> Multimedia Extension
- SSE
> Streaming SIMD Extensions
> Pentium 4 processor introduce 8 128-bit XMM registers
- quad: 4 * 16 = 64
- dq: double quad: 2 * 4 * 16 = 125
- movdqa/movdqu
- FPU provides a method for supporting signed BCD
> 8 80-bit, st0..st7, 类似栈
> 最高位作为符号位, 低 9 字节存储 BCD
> Remember that the BCD value is converted to the floating-point representation while in the FPU
- fbld
> Packed Decimal (BCD) Load (fbld)
- fbstp
> Packed Decimal (BCD) Store and Pop (fbstp)
- Floating-point format
> 科学计数法
- IEEE Standard 754
> - A sign
> - A significand
> - exponent

- fld source
> source can be a 32-, 64-, or 80-bit memory location
> flds: single; fldl: double

- fst destnation
> fsts: single; fstl: double

- x/f; x/gf
- fld1: +1.0
- fldl2t: log<sub>2</sub><sup>10</sup>
- fldl2e: log<sub>2</sub><sup>e</sup>
- fldpi: PI
- fldlg2: log<sub>10</sub><sup>2</sup>
- fldln2: log<sub>e</sub><sup>2</sup>
- fldz: +0.0
[IEEE 754](https://www.jianshu.com/p/e5d72d764f2f)
- 128-bit packed single-precision floating-point (in SSE)
- 128-bit packed double-precision floating-point (in SSE2)
- SSE3 technology, available in Pentium 4 and later processors, adds three additional instructions
> moving packed double-precision floating-point


