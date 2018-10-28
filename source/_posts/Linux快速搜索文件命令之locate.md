---
title: Linux快速搜索文件命令之locate
date: 2018-10-28 14:06:43
tags:
	- linux
categories: "linux文件搜索命令"	
---

在Linux系统中有好几个搜索相关命令（locate,whereis,which,find），locate是搜索比较快的命令,只能通过文件名搜索

不能实时搜索文件，必须文件信息更新到locate所搜索的数据库当中，才能搜索，

locate之所以搜索这么快，就是因为从数据库中搜索的，而不需要遍历目录，所以速度快，消耗小




简单体验下，搜索挺快的，要搜索刚新建的文件，必须先执行updatedb

```
root@iZbp1fczkfsaufav1m0wuiZ:~# locate host.conf
/etc/host.conf
/usr/local/share/aliyun-assist/1.0.1.226/config/host.conf
/usr/local/share/aliyun-assist/1.0.1.259/config/host.conf
/usr/share/man/man5/host.conf.5.gz
root@iZbp1fczkfsaufav1m0wuiZ:~# touch jiangyd.txt
root@iZbp1fczkfsaufav1m0wuiZ:~# ls
jiangyd.txt
root@iZbp1fczkfsaufav1m0wuiZ:~# locate jiangyd.txt 
root@iZbp1fczkfsaufav1m0wuiZ:~# updatedb
root@iZbp1fczkfsaufav1m0wuiZ:~# locate jiangyd.txt 
/root/jiangyd.txt

```


这里分别在/tmp目录，/media目录创建了jiangyd.txt，执行了updatedb,locate为什么没有搜索到,看看配置就知道了

```
root@iZbp1fczkfsaufav1m0wuiZ:~# touch /tmp/jiangyd.txt
root@iZbp1fczkfsaufav1m0wuiZ:~# updatedb
root@iZbp1fczkfsaufav1m0wuiZ:~# locate jiangyd.txt
/root/jiangyd.txt
root@iZbp1fczkfsaufav1m0wuiZ:~# touch /media/jiangyd.txt
root@iZbp1fczkfsaufav1m0wuiZ:~# updatedb
root@iZbp1fczkfsaufav1m0wuiZ:~# locate jiangyd.txt 
/root/jiangyd.txt

```


locate搜索也是有配置的,更改配置后执行updatedb生效
	
1. PRUNE_BIND_MOUNTS 这个配置如果为yes，则下面其它配置都是生效的
2. PRUNENAMES 排除某目录搜索(例如.git目录下,.svn目录,也可自定义添加)
3. PRUNEPATHS 排除搜索的路径
4. PRUNEFS  排除搜索的文件系统(linux一般是ext2,3之类的文件系统，像iso9660光盘文件系统，就是被排除了)

```
root@iZbp1fczkfsaufav1m0wuiZ:~# cat /etc/updatedb.conf 
PRUNE_BIND_MOUNTS="yes"
# PRUNENAMES=".git .bzr .hg .svn"
PRUNEPATHS="/tmp /var/spool /media /home/.ecryptfs /var/lib/schroot"
PRUNEFS="NFS nfs nfs4 rpc_pipefs afs binfmt_misc proc smbfs autofs iso9660 ncpfs coda devpts ftpfs devfs mfs shfs sysfs cifs lustre tmpfs usbfs udf fuse.glusterfs fuse.sshfs curlftpfs ecryptfs fusesmb devtmpfs"
```


它的数据库文件，通过man locate查看帮助文档，可以看到默认的数据库文件

```
FILES
       /var/lib/mlocate/mlocate.db
              The database searched by default.
```