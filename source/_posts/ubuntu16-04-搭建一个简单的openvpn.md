---
title: ubuntu16.04 搭建一个简单的openvpn
date: 2018-10-29 16:01:39
tags:
	- linux
	- vpn

---

为什么需要搭建VPN，一个主要原因是学习下，二个是公司测试需要买阿里云vpc的资源（买资源比较麻烦，需要走审批），但是这个资源需要频繁变更，例如（换系统镜像，重置）之类的，而又没有权限，买太多资源有时侯又比较浪费，自己买资源又连不上那个网络，本着学习的目标自己网上找资料搭建个vpn试试，这样我只要申请买一个资源，其它测试资源我自己买，通过VPN连过去，网络也就通了



系统版本

```
LSB Version:	core-9.20160110ubuntu0.2-amd64:core-9.20160110ubuntu0.2-noarch:security-9.20160110ubuntu0.2-amd64:security-9.20160110ubuntu0.2-noarch
Distributor ID:	Ubuntu
Description:	Ubuntu 16.04.4 LTS
Release:	16.04
Codename:	xenial
```

安装

```
apt update
apt install openssl
apt install lzop
apt install openvpn
apt install easy-rsa

```

创建目录(如果目录不存在就不需要创建)

```
mkdir /etc/openvpn
```

拷贝安装的easy-rsa到/etc/openvpn目录下

```
# find / -name "easy-rsa"
/usr/share/doc/easy-rsa
/usr/share/easy-rsa
# cp -r /usr/share/easy-rsa /etc/openvpn/
```

配置 (国家，省，城市，组织，邮件，单位)

```
# cd /etc/openvpn/easy-rsa/
# vim vars

export KEY_COUNTRY="CN"
export KEY_PROVINCE="shanghai"
export KEY_CITY="shanghai"
export KEY_ORG="zhuyunkeji"
export KEY_EMAIL="jiangyd@jiagouyun.com"
export KEY_OU="zhuyun"

```

```
# source vars
NOTE: If you run ./clean-all, I will be doing a rm -rf on /etc/openvpn/easy-rsa/keys
# ./clean-all

#一路按回车 
# ./build-ca

#一路按回车 还有2个y确认
# ./build-key-server server

#为客户端生成证书和私钥 一路按回车，还有2个y确认
# ./build-key client1

# ./build-key client2    

...
```

```
#执行该命令，需要1分钟差不多
# ./build-dh

# openvpn --genkey --secret keys/ta.key

# cp -r keys /etc/openvpn/
# ls -l /etc/openvpn/keys/
total 112
-rw-r--r-- 1 root root 5638 Oct 29 16:30 01.pem
-rw-r--r-- 1 root root 5523 Oct 29 16:30 02.pem
-rw-r--r-- 1 root root 5523 Oct 29 16:30 03.pem
-rw-r--r-- 1 root root 1765 Oct 29 16:30 ca.crt
-rw------- 1 root root 1704 Oct 29 16:30 ca.key
-rw-r--r-- 1 root root 5523 Oct 29 16:30 client1.crt
-rw-r--r-- 1 root root 1086 Oct 29 16:30 client1.csr
-rw------- 1 root root 1704 Oct 29 16:30 client1.key
-rw-r--r-- 1 root root 5523 Oct 29 16:30 client2.crt
-rw-r--r-- 1 root root 1086 Oct 29 16:30 client2.csr
-rw------- 1 root root 1704 Oct 29 16:30 client2.key
-rw-r--r-- 1 root root  424 Oct 29 16:30 dh2048.pem
-rw-r--r-- 1 root root  416 Oct 29 16:30 index.txt
-rw-r--r-- 1 root root   21 Oct 29 16:30 index.txt.attr
-rw-r--r-- 1 root root   21 Oct 29 16:30 index.txt.attr.old
-rw-r--r-- 1 root root  277 Oct 29 16:30 index.txt.old
-rw-r--r-- 1 root root    3 Oct 29 16:30 serial
-rw-r--r-- 1 root root    3 Oct 29 16:30 serial.old
-rw-r--r-- 1 root root 5638 Oct 29 16:30 server.crt
-rw-r--r-- 1 root root 1086 Oct 29 16:30 server.csr
-rw------- 1 root root 1704 Oct 29 16:30 server.key
-rw------- 1 root root  636 Oct 29 16:30 ta.key
```

```
# gzip -d /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz
# cp /usr/share/doc/openvpn/examples/sample-config-files/server.conf /etc/openvpn/
# cd /etc/openvpn/
# ls
easy-rsa  keys  server.conf  update-resolv-conf
```


配置server.conf(#,; 开头的都是注释)

```
port 1194
proto tcp
dev tun
ca /etc/openvpn/keys/ca.crt
cert /etc/openvpn/keys/server.crt
key /etc/openvpn/keys/server.key  # This file should be kept secret
dh /etc/openvpn/keys/dh2048.pem
server 10.8.0.0 255.255.255.0
ifconfig-pool-persist ipp.txt
push "route 172.16.0.0 255.255.255.0"
push "dhcp-option DNS 172.16.0.244"
client-to-client
keepalive 10 120
comp-lzo
user nobody
group nogroup
persist-key
persist-tun
status openvpn-status.log
verb 3
plugin /etc/openvpn/openvpn-plugin-auth-pam.so openvpn_mysql
client-cert-not-required
username-as-common-name

```

安装数据库

```
apt install mysql-server
```



```
mysql> CREATE DATABASE openvpn;
Query OK, 1 row affected (0.00 sec)

mysql> USE openvpn;
Database changed
mysql> GRANT ALL ON openvpn.* TO 'openvpn'@'localhost' IDENTIFIED BY 'openvpn';
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> create table user (username varchar(32),password varchar(128),active int(10) not null default 1,name varchar(32) COLLATE utf8_unicode_ci DEFAULT NULL,email char(128) COLLATE utf8_unicode_ci DEFAULT NULL,PRIMARY KEY (username));
Query OK, 0 rows affected (0.01 sec)
```

安装pam_mysql模块

```
apt install libpam-mysql
```


```
# vim /etc/pam.d/openvpn_mysql

auth            sufficient      pam_mysql.so user=openvpn passwd=openvpn host=localhost db=openvpn table=user usercolumn=username passwdcolumn=password where=active=1 crypt=1

account         required        pam_mysql.so user=openvpn passwd=openvpn host=localhost db=openvpn table=user usercolumn=username passwdcolumn=password where=active=1 crypt=1
```




```
# apt install sasl2-bin

# /etc/init.d/saslauthd restart
[ ok ] Restarting saslauthd (via systemctl): saslauthd.service.
```


通过新建的openvpn用户连接数据库，并插入数据

```
# mysql -uopenvpn -p

mysql> use openvpn

mysql> INSERT INTO user(username, password) VALUES('test', ENCRYPT('123456'));
Query OK, 1 row affected, 1 warning (0.00 sec)

mysql> exit
Bye

# 
```



出现这个错误

```
# cd /etc/pam.d/
# testsaslauthd -u test -p 123456 -s openvpn_mysql
connect() : No such file or directory
```

网上查了下，/etc/init.d/saslauthd restart  这样重启服务不行，需要如下启动服务才行

```
saslauthd -a pam
```


下面的日志,网上查了是一个bug

```

# tailf /var/log/auth.log
Oct 29 19:11:58 iZbp1fczkfsaufav1m0wuiZ saslauthd[4386]: PAM unable to dlopen(pam_mysql.so): /lib/security/pam_mysql.so: undefined symbol: make_scrambled_password
Oct 29 19:11:58 iZbp1fczkfsaufav1m0wuiZ saslauthd[4386]: PAM adding faulty module: pam_mysql.so
Oct 29 19:11:58 iZbp1fczkfsaufav1m0wuiZ saslauthd[4386]: DEBUG: auth_pam: pam_authenticate failed: Permission denied
Oct 29 19:11:58 iZbp1fczkfsaufav1m0wuiZ saslauthd[4386]: do_auth         : auth failure: [user=test] [service=openvpn] [realm=] [mech=pam] [reason=PAM auth error]
```

查看系统架构，选择修复文件

```
# uname -a
Linux iZbp1fczkfsaufav1m0wuiZ 4.4.0-117-generic #141-Ubuntu SMP Tue Mar 13 11:58:07 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

https://bugs.launchpad.net/ubuntu/xenial/+source/pam-mysql/+bug/1574900/comments/23


http://www.dinofly.com/files/linux/libpam-mysql_0.7~RC1-4ubuntu3_amd64.deb

http://www.dinofly.com/files/linux/libpam-mysql_0.7~RC1-4ubuntu3_i386.deb


安装补丁

```
# dpkg -i libpam-mysql_0.7~RC1-4ubuntu3_amd64.deb

```


再次测试

```
# cd /etc/pam.d/
# testsaslauthd -u test -p 123456 -s openvpn_mysql
0: OK "Success."
```


复制openvpn认证插件

```
# cp /usr/lib/openvpn/openvpn-plugin-auth-pam.so /etc/openvpn/
```



重启openvpn

```
# /etc/init.d/openvpn restart
[ ok ] Restarting openvpn (via systemctl): openvpn.service.
```



如果出现以下错误

```
Oct 30 16:23:42 iZbp1fczkfsaufav1m0wuiZ ovpn-server[16944]: 172.16.0.244:49328 TLS: Initial packet from [AF_INET]172.16.0.244:49328, sid=0c8ad8ad 5719ccee
Oct 30 16:23:42 iZbp1fczkfsaufav1m0wuiZ ovpn-server[16944]: 172.16.0.244:49328 TLS Error: cannot locate HMAC in incoming packet from [AF_INET]172.16.0.244:49328
Oct 30 16:23:42 iZbp1fczkfsaufav1m0wuiZ ovpn-server[16944]: 172.16.0.244:49328 Fatal TLS error (check_tls_errors_co), restarting
Oct 30 16:23:42 iZbp1fczkfsaufav1m0wuiZ ovpn-server[16944]: 172.16.0.244:49328 SIGUSR1[soft,tls-error] received, client-instance restarting
```


配置防火墙，别忘记保存


```
iptables -I INPUT -p tcp --dport 1194 -m comment --comment "openvpn" -j ACCEPT
iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -j MASQUERADE
mkdir /etc/iptables
iptables-save > /etc/iptables/iptables.conf

```






客户端配置例子

```
client
dev tun
proto tcp
remote 47.98.39.141 1194
resolv-retry infinite
nobind
persist-key
persist-tun
ca ca.crt


user nobody
group nobody

remote-cert-tls server
comp-lzo
verb 3


#for auth password security
auth-nocache #不缓存认证信息
auth-user-pass auth.txt  #密码存放位置，这样可以不用每次认证时都输入密码
reneg-sec 3600 #重新认证时间
```

密码文件例子,用户名密码

```
test
123456

```

运行openvpn客户端，使用管理身份运行

如果出现连上了openvpn,但是互相ping不通 10.8.0.1,可以看看openvpn客户端的日志，是不是路由添加失败，openvpn服务端是不是开启防火墙


这个情况就是ping不通 10.8.0.1 

```
C:\Users\jyd>route print

IPv4 路由表
===========================================================================
活动路由:
网络目标        网络掩码          网关       接口   跃点数
          0.0.0.0          0.0.0.0     10.100.3.254     10.100.3.142     11
         10.8.0.4  255.255.255.252            在链路上          10.8.0.6    276
         10.8.0.6  255.255.255.255            在链路上          10.8.0.6    276
         10.8.0.7  255.255.255.255            在链路上          10.8.0.6    276
       10.100.3.0    255.255.255.0            在链路上      10.100.3.142    266
     10.100.3.142  255.255.255.255            在链路上      10.100.3.142    266
     10.100.3.255  255.255.255.255            在链路上      10.100.3.142    266
        127.0.0.0        255.0.0.0            在链路上         127.0.0.1    306
        127.0.0.1  255.255.255.255            在链路上         127.0.0.1    306
  127.255.255.255  255.255.255.255            在链路上         127.0.0.1    306
     192.168.56.0    255.255.255.0            在链路上    192.168.56.101    266
   192.168.56.101  255.255.255.255            在链路上    192.168.56.101    266
   192.168.56.255  255.255.255.255            在链路上    192.168.56.101    266
        224.0.0.0        240.0.0.0            在链路上         127.0.0.1    306
        224.0.0.0        240.0.0.0            在链路上      10.100.3.142    266
        224.0.0.0        240.0.0.0            在链路上    192.168.56.101    266
        224.0.0.0        240.0.0.0            在链路上          10.8.0.6    276
  255.255.255.255  255.255.255.255            在链路上         127.0.0.1    306
  255.255.255.255  255.255.255.255            在链路上      10.100.3.142    266
  255.255.255.255  255.255.255.255            在链路上    192.168.56.101    266
  255.255.255.255  255.255.255.255            在链路上          10.8.0.6    276
===========================================================================
永久路由:
  无

```

这个就是可以ping通10.8.0.1, 在第二行多一条纪录 

10.8.0.0    255.255.255.0         10.8.0.5         10.8.0.6     20

```
IPv4 路由表
===========================================================================
活动路由:
网络目标        网络掩码          网关       接口   跃点数
          0.0.0.0          0.0.0.0     10.100.3.254     10.100.3.142     11
         10.8.0.0    255.255.255.0         10.8.0.5         10.8.0.6     20
         10.8.0.4  255.255.255.252            在链路上          10.8.0.6    276
         10.8.0.6  255.255.255.255            在链路上          10.8.0.6    276
         10.8.0.7  255.255.255.255            在链路上          10.8.0.6    276
       10.100.3.0    255.255.255.0            在链路上      10.100.3.142    266
     10.100.3.142  255.255.255.255            在链路上      10.100.3.142    266
     10.100.3.255  255.255.255.255            在链路上      10.100.3.142    266
        127.0.0.0        255.0.0.0            在链路上         127.0.0.1    306
        127.0.0.1  255.255.255.255            在链路上         127.0.0.1    306
  127.255.255.255  255.255.255.255            在链路上         127.0.0.1    306
       172.16.0.0    255.255.255.0         10.8.0.5         10.8.0.6     20
     192.168.56.0    255.255.255.0            在链路上    192.168.56.101    266
   192.168.56.101  255.255.255.255            在链路上    192.168.56.101    266
   192.168.56.255  255.255.255.255            在链路上    192.168.56.101    266
        224.0.0.0        240.0.0.0            在链路上         127.0.0.1    306
        224.0.0.0        240.0.0.0            在链路上      10.100.3.142    266
        224.0.0.0        240.0.0.0            在链路上    192.168.56.101    266
        224.0.0.0        240.0.0.0            在链路上          10.8.0.6    276
  255.255.255.255  255.255.255.255            在链路上         127.0.0.1    306
  255.255.255.255  255.255.255.255            在链路上      10.100.3.142    266
  255.255.255.255  255.255.255.255            在链路上    192.168.56.101    266
  255.255.255.255  255.255.255.255            在链路上          10.8.0.6    276
===========================================================================
永久路由:
  无
```
