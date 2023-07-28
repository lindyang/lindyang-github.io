---
title: "Location"
date: 2023-07-28T15:56:18+08:00
tags: [nginx]
---


location 的检查顺序是先`前缀`，再`正则`，但终止优先级是：
1. 首先精确匹配 =
2. 其次前缀匹配 ^~
3. 其次是按文件中顺序的正则匹配
4. 然后匹配不带任何修饰的前缀匹配。
5. 最后是交给 / 通用匹配
⚠️  `检查顺序`和`终止优先级`不是一回事



为什么`正则匹配`在`前缀匹配`之前，重点参考:
> The search of regular expressions `terminates` on the first match, and the corresponding configuration is used. If no match with a regular expression is found then the configuration of the `prefix location remembered earlier is used`.
> #### 不考虑修饰符`=`和`^~`时:
> - 先检查`前缀匹配`, 是为找到`最长前缀匹配`。
> - 接着查找正则匹配，找到，那么就 `terminates`
> - 不幸无正则匹配，才去吃回头草（最长前缀匹配）

