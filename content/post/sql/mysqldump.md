---
title: "Mysqldump"
date: 2023-07-28T14:49:12+08:00
tags: [sql]
---


```
mysqldump \
    db_name \
    --complete-insert \
    --compress \
    --skip-column-statistics \
    --no-create-db \
    --no-create-info \
    -h 127.0.0.1 \
    -u dummy \
    -p"dummy@123" \
    --ssl-mode=DISABLED \
    --extended-insert \
    --tables \
    t1 t2 \
    > db_name-t1-t2.sql;
```
