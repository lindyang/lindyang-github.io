---
title: "Rrtpc"
date: 2023-08-24T14:44:18+08:00
tags: [misc]
---


```
[Unit]
Description=SoftEther VPN Client
After=network-online.target systemd-resolved.service
Wants=network-online.target

[Service]
Type=simple
ExecStart=/usr/sbin/sstpc --cert-warn --save-server-route --user yangld --password PASSWORD IP:PORT usepeerdns require-mschap-v2 noauth noipdefault nobsdcomp nodeflate
ExecStartPost=/bin/bash -c 'sleep 7; ip route add 192.168.0.121/32 dev ppp0;'
ExecStop=pkill sstpc
Restart=Always

[Install]
WantedBy=multi-user.target
```


