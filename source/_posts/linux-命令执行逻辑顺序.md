---
title: linux 命令执行逻辑顺序
date: 2018-11-05 11:48:57
tags:
	- linux
---

### ;

Linux中，;(英文状态)可以连接多个命令，无论如何每个命令都会执行

```
# pwd;who
/root
jiangyd  pts/0        2018-11-05 11:44 (192.168.56.1)


# ppp;who
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
jiangyd  pts/0        2018-11-05 11:44 (192.168.56.1)


# pwd;www
/root
No command 'www' found, did you mean:
 Command 'wwl' from package 'wwl' (universe)
 Command 'wwt' from package 'wit' (universe)
 Command 'dwww' from package 'dwww' (universe)
 Command 'wcw' from package 'winswitch' (universe)
www: command not found


# pwd;ifconfig;ls;cat /etc/host.conf
/root
eth0      Link encap:Ethernet  HWaddr 08:00:27:ae:ed:52
          inet addr:10.100.3.178  Bcast:10.100.3.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:feae:ed52/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:4347 errors:0 dropped:0 overruns:0 frame:0
          TX packets:4065 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:653931 (653.9 KB)  TX bytes:369259 (369.2 KB)

eth1      Link encap:Ethernet  HWaddr 08:00:27:fd:5f:42
          inet addr:192.168.56.102  Bcast:192.168.56.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fefd:5f42/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:490 errors:0 dropped:0 overruns:0 frame:0
          TX packets:372 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:42685 (42.6 KB)  TX bytes:50505 (50.5 KB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:10 errors:0 dropped:0 overruns:0 frame:0
          TX packets:10 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:580 (580.0 B)  TX bytes:580 (580.0 B)

tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          inet addr:10.8.0.1  P-t-P:10.8.0.2  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

a.sql   demo.conf        install-3.0.1-198.sh  install.sh  sharedr  test.conf
cc.txt  guacinstall.log  install-3.0.1-216.sh  log         test     windows
# The "order" line is only used by old versions of the C library.
order hosts,bind
multi on
```


### &&

Linux中，&&(英文状态) 表示逻辑与,前面的命令执行成功，后面的命令才会执行

```

# pwd && ls
/root
a.sql   demo.conf        install-3.0.1-198.sh  install.sh  sharedr  test.conf
cc.txt  guacinstall.log  install-3.0.1-216.sh  log         test     windows


# ppp && ls
No command 'ppp' found, but there are 19 similar ones
ppp: command not found


# pwd && lll
/root
No command 'lll' found, did you mean:
 Command 'llt' from package 'storebackup' (universe)
 Command 'lli' from package 'llvm-runtime' (universe)
 Command 'llc' from package 'llvm' (universe)
lll: command not found
```


### ||

linux中,||(英文状态) 表示逻辑或,前面的没有执行成功，就执行后面的，前面的执行成功了，就不执行后面的

```

# pwd || ls
/root



# ppp || ls
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
a.sql   demo.conf        install-3.0.1-198.sh  install.sh  sharedr  test.conf
cc.txt  guacinstall.log  install-3.0.1-216.sh  log         test     windows


# ppp || lll
No command 'ppp' found, but there are 19 similar ones
ppp: command not found
No command 'lll' found, did you mean:
 Command 'llt' from package 'storebackup' (universe)
 Command 'llc' from package 'llvm' (universe)
 Command 'lli' from package 'llvm-runtime' (universe)
lll: command not found

```
