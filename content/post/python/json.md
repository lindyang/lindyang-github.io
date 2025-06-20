---
title: "json"
date: 2023-07-28T15:52:36+08:00
tags: [python]
---

```
如果选择 json 操作上比较复杂, 需要使用类似的语法
update device 
set obj = COALESCE(obj, jsonb_build_object) || jsonb_build_object('key', column) || jsonb_build_object...
where id=?

更改字段:
SET obj = jsonb_set(obj, '{obj,type}', '"Los Angeles"')

删除字段
SET obj = obj - 'timeout'


jsonb_set(target jsonb, path text[], new_value jsonb[, create_missing boolean])


使用 || 合并操作符
使用 jsonb_set 或 - 操作符删除字段：使用 jsonb_set 设置为 null

->> 可以去掉字符串中两边的引号


array_agg: '{1, 2, 3}';
ARRAY[1,2,3];
'{1,2,3}'::int[];

NULLIF;
```
