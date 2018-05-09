---
title: Linux下使用sed替换dns地址
date: 2018-05-09 11:05:50
tags:
 － sed
---


#### sed替换/etc/resolv.conf文件的dns地址

```
# cat /etc/resolv.conf
nameserver 114.114.114.114
nameserver 8.8.8.8

# sed -i 's/[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}/172.16.0.244/g' /etc/resolv.conf

# cat /etc/resolv.conf
nameserver 172.16.0.244
nameserver 172.16.0.244
```
