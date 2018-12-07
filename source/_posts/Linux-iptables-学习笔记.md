---
title: Linux iptables 学习笔记
date: 2018-11-07 09:34:31
tags:
	- linux
---

iptables


四张表，五链路

1. filter 一般的过滤功能
2. nat 用于nat功能(端口映射,地址映射等)
3. mangle 用于对特定数据包的修改
4. raw 优先级最高

默认表是filter(没有指定表的时候就是filter表)

表的优先级

raw>mangle>nat>filter


查看iptables规则

```
# iptables -L
Chain INPUT (policy ACCEPT)
target     prot opt source               destination

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination
```

```
# iptables -I INPUT -p tcp --dport 80 -j REJECT
# iptables -nL
Chain INPUT (policy ACCEPT)
target     prot opt source               destination
REJECT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:80 reject-with icmp-port-unreachable

Chain FORWARD (policy ACCEPT)
target     prot opt source               destination

Chain OUTPUT (policy ACCEPT)
target     prot opt source               destination

```