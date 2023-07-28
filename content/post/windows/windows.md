---
title: "Windows"
date: 2023-07-28T14:46:58+08:00
tags: [windows]
---


![image.png](https://upload-images.jianshu.io/upload_images/14001410-8961354568343fe2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/14001410-69c084b55664c548.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### windows上的文件 NFS 挂载
```
mount -t cifs //192.168.1.4/dir /mnt \
  -o noatime \
  -o nodiratime \
  -o username=xxx,password=xxx,rw,uid=0,gid=0,dir_mode=0777,file_mode=0777
```

### cygwin 进入 Vmware 共享文件
```
net use H: '\\vmware-host\Shared Folders'
cd /cygdrive/h/
```

sudo ip route add 39.100.82.7 via 192.168.3.1 dev enp0s31f6 proto static

宿主机利用在虚拟机中建立的VPN加密隧道连接内网
