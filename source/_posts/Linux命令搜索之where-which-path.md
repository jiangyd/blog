---
title: 'Linux命令搜索之where,which,path'
date: 2018-10-28 18:53:23
tags:
	- linux
categories: "linux命令搜索命令"		
---


命令搜索命令，指的是搜索系统的命令，例如ls,pwd


whereis 命令与which不同的是，会把帮助文档也搜索出来

```
# whereis pwd
pwd: /bin/pwd /usr/include/pwd.h /usr/share/man/man1/pwd.1.gz
# whereis ls
ls: /bin/ls /usr/share/man/man1/ls.1.gz

# which ls
/bin/ls
# which pwd
/bin/pwd
```


那么whereis,which的搜索范围是那里,环境变量PATH就是它的搜索范围

```
# echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games
```


通过whereis，which为什么没有搜索出cd命令，因为cd命令是shell内置的，而ls，pwd是系统默认安装的命令

```
# whereis cd
cd:
# which cd
# 
```