---
title: "Fdis"
date: 2023-07-28T15:50:10+08:00
tags: [bash]
---


`1049kB` 对应 start 的值，是多少就写多少
```
#!/bin/bash
set -eux

# referer: https://help.aliyun.com/document_detail/25452.html?spm=a2c4g.11186623.2.30.464a7f67a568oQ#section-oi9-qp8-f04


fdisk -lu /dev/vdb
blkid /dev/vdb1
e2fsck -n /dev/vdb1
mount | grep /dev/vdb

systemctl stop elasticsearch.service
umount /dev/vdb1
systemctl stop data.mount
mount | grep /dev/vdb

cat <<"EOF" | parted /dev/vdb
print
Fix
rm 1
mkpart primary 1049kB 100%
print
quit
EOF

fsck -f /dev/vdb1
resize2fs /dev/vdb1
systemctl start data.mount
mount /dev/vdb1
systemctl start elasticsearch.service
```
