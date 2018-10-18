---
title: linux访问Windows的共享文件夹
date: 2018-10-08 19:55:40
tags:
	- samba
	- mount
---

在Windows下要访问Linux下的共享文件夹，需要Linux配置samba服务器，然后Windows直接在地址栏输入\\服务器ip地址访问即可
在Linux下，访问Windows的共享文件夹，可以使用smbclient,也可以使用mount.cifs

#### 这里先介绍sambaclient，samba的客户端软件
1.	安装
```
apt-get install smbclient

```

2. smbclient像FTP一样连接windows的共享文件夹,出现smb: \> 表示连接成功了,输入help显示所有命令

输入help 具体命令 查看某命令详细说明

```
# smbclient //192.168.56.101/server
WARNING: The "syslog" option is deprecated
Enter root's password:
OS=[Windows 7 Ultimate 7601 Service Pack 1] Server=[Windows 7 Ultimate 6.1]
smb: \>
smb: \> help
?              allinfo        altname        archive        backup
blocksize      cancel         case_sensitive cd             chmod
chown          close          del            dir            du
echo           exit           get            getfacl        geteas
hardlink       help           history        iosize         lcd
link           lock           lowercase      ls             l
mask           md             mget           mkdir          more
mput           newer          notify         open           posix
posix_encrypt  posix_open     posix_mkdir    posix_rmdir    posix_unlink
print          prompt         put            pwd            q
queue          quit           readlink       rd             recurse
reget          rename         reput          rm             rmdir
showacls       setea          setmode        scopy          stat
symlink        tar            tarmode        timeout        translate
unlock         volume         vuid           wdel           logon
listconnect    showconnect    tcon           tdis           tid
logoff         ..             !
smb: \> help get
HELP get:
	<remote name> [local name] get a file
```

3. 试下常用的命令，上传下载吧

下载

```
smb: \> ls
  .                                   D        0  Wed Oct 10 09:07:43 2018
  ..                                  D        0  Wed Oct 10 09:07:43 2018
  110                                 D        0  Wed Mar 28 16:48:06 2018
  a.txt                               A  6413610  Mon Sep 10 17:50:27 2018

smb: \> get a.txt /home/jiangyd/a.txt
getting file \a.txt of size 6413610 as /home/jiangyd/a.txt (90772.2 KiloBytes/sec) (average 90772.3 KiloBytes/sec)  
```

上传

```
smb: \> put /home/jiangyd/d.sql d.sql
putting file /home/jiangyd/d.sql as \d.sql (9.4 kb/s) (average 9.4 kb/s)
smb: \> ls
  .                                   D        0  Wed Oct 10 09:15:09 2018
  ..                                  D        0  Wed Oct 10 09:15:09 2018
  110                                 D        0  Wed Mar 28 16:48:06 2018
  a.txt                               A  6413610  Mon Sep 10 17:50:27 2018
  d.sql                               A       48  Wed Oct 10 09:15:09 2018

		1309951 blocks of size 4096. 144043 blocks available
smb: \> put /home/jiangyd/d.sql 110\d.sql
putting file /home/jiangyd/d.sql as \110\d.sql (11.7 kb/s) (average 10.4 kb/s)
```


4. 带用户名密码访问

```
root@jiangydpc:/home/jiangyd# smbclient //192.168.56.101/server --user=jyd 123456
WARNING: The "syslog" option is deprecated
Domain=[JYD-PC] OS=[Windows 7 Ultimate 7601 Service Pack 1] Server=[Windows 7 Ultimate 6.1]
smb: \> ls
  .                                   D        0  Wed Oct 10 09:15:09 2018
  ..                                  D        0  Wed Oct 10 09:15:09 2018
  110                                 D        0  Wed Oct 10 09:15:19 2018
  a.txt                               A  6413610  Mon Sep 10 17:50:27 2018
  d.sql                               A       48  Wed Oct 10 09:16:29 2018

		1309951 blocks of size 4096. 144043 blocks available
smb: \> exit
```

#### cifs-utils 
 mount.cifs - mount using the Common Internet File System (CIFS)  网络文件系统

 1. 安装

 ```
 apt-get install cifs-utils
 ```

 2. 把Windows共享文件夹csos，挂载到当前路径下Windows目录，使用用户名jyd,密码123456

 ```
root@ubuntu:/home/jiangyd# mount.cifs //192.168.0.107/csos ./windows/ -o user=jyd,pass=123456
root@ubuntu:/home/jiangyd# ls
test  windows
root@ubuntu:/home/jiangyd# cd windows/
root@ubuntu:/home/jiangyd/windows# ls
bd  csos  csos.zip
root@ubuntu:/home/jiangyd/windows# touch a.txt
root@ubuntu:/home/jiangyd/windows# ls
a.txt  bd  csos  csos.zip
 ```

 3. 取消挂载

 ```

root@ubuntu:/home/jiangyd# umount ./windows/
root@ubuntu:/home/jiangyd# cd windows/
root@ubuntu:/home/jiangyd/windows# ls
root@ubuntu:/home/jiangyd/windows# 
 ```