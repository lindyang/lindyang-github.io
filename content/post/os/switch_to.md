---
title: "Switch_to"
date: 2023-07-28T15:45:09+08:00
tags: [os]
---


schedule ->
```
context_switch(rq, prev, next) {
       switch_to(prev, next, prev);  // __宏__

       finish_task_switch(this_rq(), prev);
}
```

#define switch_to(prev, next, last)
```
"=a" (last)
```
- 为什么宏 switch_to 需要有 last？
因为 context_switch 中的 finish_task_switch 函数需要 prev。
难道 context_switch 不能直接引用 prev?
对不起，不行？
> switch_to 宏执行完毕以后，esp，ebp 已经改变，而 context_switch 中的 prev、next 是前一个进程的局部变量(在其栈中，一般通过)


