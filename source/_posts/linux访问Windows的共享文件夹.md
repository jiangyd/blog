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

```

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