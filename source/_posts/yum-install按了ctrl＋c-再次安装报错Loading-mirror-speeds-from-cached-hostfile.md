---
title: 'yum install按了ctrl＋c,再次安装报错Loading mirror speeds from cached hostfile'
date: 2018-10-11 10:56:00
tags:
	- CentOS
	- yum
---


#### yum install的时候，我按了ctrl＋c，停掉了当前的安装

然后我再次安装软件的时候，报错了

```
[root@iZm5egfy7v1z256fhrdir5Z ~]# yum install lrzsz
已加载插件：fastestmirror
设置安装进程
Loading mirror speeds from cached hostfile
错误：database disk image is malformed
```

看这个错误，好像与缓存有关系,执行清楚缓存，再次安装软件正常

```
[root@iZm5egfy7v1z256fhrdir5Z ~]# yum clean all
已加载插件：fastestmirror
Cleaning repos: base epel extras updates
清理一切
Cleaning up list of fastest mirrors
[root@iZm5egfy7v1z256fhrdir5Z ~]# yum install lrzsz
已加载插件：fastestmirror
设置安装进程
Determining fastest mirrors
base                                                                  | 3.7 kB     00:00
base/primary_db                                                       | 4.7 MB     00:00
epel                                                                  | 3.2 kB     00:00
epel/primary                                                          | 3.2 MB     00:00
epel                                                                             12516/12516
extras                                                                | 3.4 kB     00:00
extras/primary_db                                                     |  26 kB     00:00
updates                                                               | 3.4 kB     00:00
updates/primary_db                                                    | 1.8 MB     00:00
解决依赖关系
--> 执行事务检查
---> Package lrzsz.x86_64 0:0.12.20-27.1.el6 will be 安装
--> 完成依赖关系计算

依赖关系解决

=============================================================================================
 软件包            架构               版本                            仓库              大小
=============================================================================================
正在安装:
 lrzsz             x86_64             0.12.20-27.1.el6                base              71 k

事务概要
=============================================================================================
Install       1 Package(s)

总下载量：71 k
Installed size: 159 k
确定吗？[y/N]：
```
