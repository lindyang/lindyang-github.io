---
title: "As"
date: 2023-07-28T15:59:13+08:00
tags: [as]
---


.code32 才可以使用 pushl cpuid2.s

- as --32 -o cpuid2.o cpuid2.s
- ld -m elf_i386 --dynamic-linker /lib/ld-linux.so.2 -lc -o cpuid2 cpuid2.o
- gcc -m32 -o cpuid2 cpuid2.s


lodsb: ds:[esi] -> al

stosb: al -> es:[edi]

SCASB: 比较 [edi] 和 al

Compares a byte in memory with the AL register value
AT&T: cmp (di), al, 是 al - (di)呢

asm (“assembly code” : output locations : input operands : changed registers);

如果不希望汇编语句被 GCC 优化而作修改,就需要在 asm 符号后面添加关键词 volatile
关键词 volatile 也可以放在函数名前来修饰函数,用来通知 gcc 编译器该函数不会返回:
volatile void do_exit(long code);
static inline volatile void oom(void)

如果输入寄存器的代码是 0 或为空时,则说明使用与相应输出一样的寄存器;  空验证失败

“constraint”(variable)

q: EAX,EBX,ECX,EDX

r: general-purpose register; EAX,EBX,ECX,EDX,EBP,ESP,ESI,EDI

EAX为累加器，ECX为计数器，EBX，EBP为基址寄存器，ESI,EDI为变址寄存器，EBP还可以是基指针，ESP为堆栈指针

g: Use any register or memory

o:  Use an offset memory location(使用内存地址并可以加偏移值)

#include<stdio.h>

int main(void){

  char __res; int p[] = {0x970F};

  char b = 'b'; // 98

  char c = 'c'; // 99

  __asm__("movb %%cs:1%1, %%al" :"=a"(__res) :"o"(b));

  return __res;

}

/*

***-19****-18****-17****-16****-15****-14***-13****

|   98   |    99   |          |  0x0F  0x97  0x00  0x00  |
movl $38671, -16(%ebp)
movb $98, -19(%ebp)
movb $99, -18(%ebp)
movb %cs:1-19(%ebp), %al  # 99

*/

changed registers:

using the full register names;
Using the percent sign with the register name is optional

把那些没有在输出/输入寄存器部分列出,但是在汇编语句中明确使用到或隐含使用到的寄存器.

如果在内联汇编中使用了没有在输入输出中定义的任何内存位置，必须标记为被破坏的。在改动的寄存器列表中使用”memory“通知编译器这个内存位置在内联汇编中被改动。

The compiler assumes that registers used in the input and output values will change, and handles thataccordingly. You do not need to include these values in the changed registers list. In fact, if you do, itwill produce an error message:
p.s. error: ‘asm’ operand has impossible constraints

下面是 output register

+ The operand can be both read from and written to.

= The operand can only be written to.

% The operand can be switched with the next operand if necessary.

& The operand can be deleted and reused before the inline functionscomplete.

早期会变的(earlyclobber)操作数。表示在使用完操作数之前,内容会被修改

earlyclobber

earlyclobber的意思是说，某个输出操作数，可能在gcc尚未用完所有的输入操作数之前，就已经被写入(inline asm的输出操作)值了。 换言之，gcc尚未使用完全部的输入操作数的时候，就已经对某些输出操作数产生了输出操作。

asm("movl $10, %0; mov $20, %1;": "=&r"(v1): "r"(v2));

“&” 是说明 Register should be used for output only

http://blog.sina.com.cn/s/blog_63fe270801014w5u.html

stos

STOS指令的作用是将eax中的值拷贝到ES:EDI指向的地址

https://zhuanlan.zhihu.com/p/27586298

防止头文件重复引入

文件开头加:
＃pragma once

#ifndef _TEST_H
#define _TEST_H
...
#endif

