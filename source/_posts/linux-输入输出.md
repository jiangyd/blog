---
title: linux 输入输出
date: 2018-11-05 13:07:21
tags:
	- linux
---

输入输出

0 标准输入 /dev/stdin
1 标准输出 /dev/stdout
2 标准错误输出 /dev/stderr

输出到黑洞  /dev/null



### >

把执行的结果输出到result.txt （result.txt文件如果不存在，则会创建，如果存在，则会覆盖里面的内容，一般不常用）

```
# ifconfig > result.txt
eth0      Link encap:Ethernet  HWaddr 08:00:27:ae:ed:52
          inet addr:10.100.3.178  Bcast:10.100.3.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:feae:ed52/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:14181 errors:0 dropped:0 overruns:0 frame:0
          TX packets:12771 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2094728 (2.0 MB)  TX bytes:1183057 (1.1 MB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:fd:5f:42
          inet addr:192.168.56.102  Bcast:192.168.56.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fefd:5f42/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1351 errors:0 dropped:0 overruns:0 frame:0
          TX packets:885 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:112875 (112.8 KB)  TX bytes:115237 (115.2 KB)
```

### >>

把执行的结果输出到result.txt，采用追加的方式写入（常用）

```
# pwd >> result.txt
# ifconfig >> result.txt
# cat result.txt
/root
eth0      Link encap:Ethernet  HWaddr 08:00:27:ae:ed:52
          inet addr:10.100.3.178  Bcast:10.100.3.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:feae:ed52/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:15183 errors:0 dropped:0 overruns:0 frame:0
          TX packets:13621 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2241609 (2.2 MB)  TX bytes:1261432 (1.2 MB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:fd:5f:42
          inet addr:192.168.56.102  Bcast:192.168.56.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fefd:5f42/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:1640 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1045 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:136657 (136.6 KB)  TX bytes:135957 (135.9 KB)

```


以下操作，你会发现，输出并没有输入到文件（log.log）,原因很简单，lll执行报错，输出内容属于标准错误输出

```
# lll >> log.log
No command 'lll' found, did you mean:
 Command 'llc' from package 'llvm' (universe)
 Command 'llt' from package 'storebackup' (universe)
 Command 'lli' from package 'llvm-runtime' (universe)
lll: command not found
# cat log.log
```


### 2>>

把标准错误输出重定向到文件中

```
# lll 2>>log.log
# cat log.log
No command 'lll' found, did you mean:
 Command 'llt' from package 'storebackup' (universe)
 Command 'llc' from package 'llvm' (universe)
 Command 'lli' from package 'llvm-runtime' (universe)
lll: command not found

```

###  &>> (追加写入,常用)， &>（覆盖文件，不常用）

把标准输出与标准错误输出到同一个文件

```
# pwd &>> log.log
# ppp &>> log.log
# cat log.log
/root
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
```

另一种写法，效果一样 (标准输出到log.log,标准错误输出流转到标准输出，标准输出再到文件log.log)

```
# pwd >> log.log 2>&1
# ppp >> log.log 2>&1
# cat log.log
/root
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
```

### 标准输出，与标准错误输出到不同文件

```
# pwd >>log.log 2>>error.log
# cat error.log
# cat log.log
/root

# ppp >>log.log 2>>error.log
# cat error.log
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
# cat log.log
/root
```


### 输出到黑洞

不管多少内容，全都可以往/dev/null里面丢，输出到黑洞等于丢弃这些输出

```
# ifconfig > /dev/null
```