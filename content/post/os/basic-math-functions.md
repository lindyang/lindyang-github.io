---
title: "Basic Math Functions"
date: 2023-07-28T16:02:54+08:00
tags: [as]
---


- The carry and overflow flags are set relative to the data size used in the addition
- adc source, destination
- movl data+4, %ebx
- printf("%qd")
> pushl 高位
> pushl 低位

 `有符号数的加减是否影响 CF?`
- NEG
> NEG命令无论你是否为正负数，都会按照取反+1或用0减去这个数的二进制的办法去计算

- The carry flag was set when the result was less than zero
- SBB instruction utilizes the carry and overflow flags in multibyte subtractions to implement the borrow feature across data boundaries
- increment (INC) and decrement (DEC) an unsigned integer

- mul source
> two unsigned integers
> AL, AX, or EAX
> DX:AX
> EDX:EAX

- using indexed memory access

```
IMUL instruction can be used by both signed and unsigned integers,
although you must be careful that the result does not use the most significant bit of the destination. For larger values,
the IMUL instruction is only valid for signed integers
```
> - imul source
> - imul source, destination
> > source can be a 16- or 32-bit register or value in memory, and destination must be a 16- or 32-bit general-purpose register
> - imul multiplier, source, destination
> > where multiplier is an immediate value, source is a 16- or 32-bit register or value in memory, and destination must be a general-purpose register

- div
> dividend in AX, DX:AX, EDX:EAX
> 低位存储商
- idiv
>  only one format for the IDIV instruction
> For signed integer division, the sign of the remainder is always the sign of the dividend


SAL (shift arithmetic left)
SHL (shift logical left)
```
sal destination
sal %cl, destination
sal shifter, destination
```
>  - destination operand can be an 8-, 16-, or 32-bit register or value in memory
> - Any bits that are shifted out of the data size are first placed in the carry
flag, and then dropped in the next shift

- SHR
- SAR
>  first moved to the carry flag, and then
shifted out

- ROL
- ROR
- RCL
- RCR

- AAA
- AAS
- AAM
- AAD
> only AAD 用在 DIV 之前
> 都使用默认的 AL register

- Packed BCD arithmetic
> - DAA: Adjusts the result of the ADD or ADC instructions
> - DAS: Adjusts the result of the SUB or SBB instructions


