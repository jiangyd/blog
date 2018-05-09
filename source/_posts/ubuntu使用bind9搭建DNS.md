---
title: ubuntu使用bind9搭建DNS
date: 2018-05-07 12:12:48
tags: 
 - dns
 - bind9
---

#### 安装bind9
```
$ sudo apt-get update
$ sudo apt-get install bind9
```
#### 默认配置文件路径
```
$ pwd
/etc/bind
```


#### 添加域名正反向解析配置

```
$ sudo vim named.conf.default-zones

zone "jiangyd.cn" {
        type master;
        file "/etc/bind/db.jiangyd.cn";
};

zone "56.168.192.in-addr.arpa" {
	type master;
	file "/etc/bind/192.168.56.zone";
};


zone "ipad.jiangyd.com.cn" {
	type master;
	file "/etc/bind/ipad.jiangyd.com.cn";
};
```

> 正向解析

```
# cat db.jiangyd.cn
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	jiangyd.cn. root.localhost. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	jiangyd.cn.
@	IN	A	127.0.0.1
@	IN	AAAA	::1

www     IN      A      192.168.56.102
bbs     IN      A      192.168.56.102
music   IN      A      192.168.56.1
```

> 反向解析

```
# cat 192.168.56.zone
;
; BIND reverse data file for local loopback interface
;
$TTL	604800
@	IN	SOA	jiangyd.cn. root.localhost. (
			      1		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS     jiangyd.cn.
102     IN      PTR    www.jiangyd.cn.
102     IN      PTR    bbs.jiangyd.cn.
1       IN      PTR    music.jiangyd.cn.
```


> 有的应用需要为不同用户分配不同域名，试试泛解析

```
# cat ipad.jiangyd.com.cn
;
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	ipad.jiangyd.com.cn. root.localhost. (
			      2		; Serial
			 604800		; Refresh
			  86400		; Retry
			2419200		; Expire
			 604800 )	; Negative Cache TTL
;
@	IN	NS	ipad.jiangyd.com.cn.
@	IN	A	127.0.0.1
@	IN	AAAA	::1


www  IN  A  192.168.56.1
bbs  IN  A  192.168.56.102
*    IN  A  192.168.56.1

```

> 正反向解析测试

```
root@jiangydpc:/etc/bind# ping www.jiangyd.cn
PING www.jiangyd.cn (192.168.56.102) 56(84) bytes of data.
64 bytes from dns.jiangyd.cn (192.168.56.102): icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from dns.jiangyd.cn (192.168.56.102): icmp_seq=2 ttl=64 time=0.026 ms
^C
--- www.jiangyd.cn ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.026/0.047/0.069/0.022 ms
root@jiangydpc:/etc/bind# ping bbs.jiangyd.cn
PING bbs.jiangyd.cn (192.168.56.102) 56(84) bytes of data.
64 bytes from www.jiangyd.cn (192.168.56.102): icmp_seq=1 ttl=64 time=0.010 ms
64 bytes from www.jiangyd.cn (192.168.56.102): icmp_seq=2 ttl=64 time=0.023 ms
^C
--- bbs.jiangyd.cn ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.010/0.016/0.023/0.007 ms
root@jiangydpc:/etc/bind# ping music.jiangyd.cn
PING music.jiangyd.cn (192.168.56.1) 56(84) bytes of data.
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=1 ttl=64 time=0.277 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=2 ttl=64 time=0.355 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=3 ttl=64 time=0.203 ms
^C
--- music.jiangyd.cn ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.203/0.278/0.355/0.063 ms
root@jiangydpc:/etc/bind# host -t PTR 192.168.56.1
1.56.168.192.in-addr.arpa domain name pointer music.jiangyd.cn.
root@jiangydpc:/etc/bind# host -t PTR 192.168.56.102
102.56.168.192.in-addr.arpa domain name pointer dns.jiangyd.cn.
102.56.168.192.in-addr.arpa domain name pointer www.jiangyd.cn.
102.56.168.192.in-addr.arpa domain name pointer bbs.jiangyd.cn.
```

> 泛解析,前面域名可以随便填写了

```
root@jiangydpc:/etc/bind# ping wqewweq.ipad.jiangyd.com.cn
PING wqewweq.ipad.jiangyd.com.cn (192.168.56.1) 56(84) bytes of data.
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=1 ttl=64 time=0.295 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=2 ttl=64 time=0.358 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=3 ttl=64 time=0.246 ms
^C
--- wqewweq.ipad.jiangyd.com.cn ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.246/0.299/0.358/0.050 ms
root@jiangydpc:/etc/bind# ping www.ipad.jiangyd.com.cn
PING www.ipad.jiangyd.com.cn (192.168.56.1) 56(84) bytes of data.
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=1 ttl=64 time=0.194 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=2 ttl=64 time=0.289 ms
^C
--- www.ipad.jiangyd.com.cn ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.194/0.241/0.289/0.049 ms
root@jiangydpc:/etc/bind# ping bbs.ipad.jiangyd.com.cn
PING bbs.ipad.jiangyd.com.cn (192.168.56.102) 56(84) bytes of data.
64 bytes from bbs.jiangyd.cn (192.168.56.102): icmp_seq=1 ttl=64 time=0.019 ms
64 bytes from bbs.jiangyd.cn (192.168.56.102): icmp_seq=2 ttl=64 time=0.030 ms
^C
--- bbs.ipad.jiangyd.com.cn ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.019/0.024/0.030/0.007 ms
root@jiangydpc:/etc/bind# ping 56789.ipad.jiangyd.com.cn
PING 56789.ipad.jiangyd.com.cn (192.168.56.1) 56(84) bytes of data.
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=1 ttl=64 time=0.343 ms
64 bytes from music.jiangyd.cn (192.168.56.1): icmp_seq=2 ttl=64 time=0.306 ms
^C
--- 56789.ipad.jiangyd.com.cn ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.306/0.324/0.343/0.025 ms
```


> 查看日志
我的系统是ubuntu 14.04 

```
tailf /var/log/syslog
```

> bind9 重启，停止，状态,及端口

```
# /etc/init.d/bind9 status
 * bind9 is running
root@jiangydpc:/etc/bind# /etc/init.d/bind9 stop
 * Stopping domain name service... bind9                                                                                                     waiting for pid 4039 to die
                                                                                                                                      [ OK ]
root@jiangydpc:/etc/bind# /etc/init.d/bind9 start
 * Starting domain name service... bind9                                                                                              [ OK ]
root@jiangydpc:/etc/bind# /etc/init.d/bind9 restart
 * Stopping domain name service... bind9                                                                                              [ OK ]
 * Starting domain name service... bind9                                                                                              [ OK ]
root@jiangydpc:/etc/bind# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 192.168.56.102:53       0.0.0.0:*               LISTEN      4133/named
tcp        0      0 10.100.3.52:53          0.0.0.0:*               LISTEN      4133/named
tcp        0      0 127.0.0.1:53            0.0.0.0:*               LISTEN      4133/named
tcp        0      0 127.0.0.1:953           0.0.0.0:*               LISTEN      4133/named
tcp        0      0 127.0.0.1:8123          0.0.0.0:*               LISTEN      1229/polipo
tcp        0      0 0.0.0.0:3333            0.0.0.0:*               LISTEN      1092/sshd
tcp6       0      0 :::53                   :::*                    LISTEN      4133/named
```