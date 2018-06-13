---
title: linux普通用户增加sudo权限
date: 2018-05-26 15:31:43
tags:
 - sudo 
---


普通用户执行sudo没有权限

```
$ sudo apt-get update
sudo: unable to resolve host dest1
[sudo] password for test: 
test is not in the sudoers file.  This incident will be reported.
```

> 解决办法: 切换到超管用户编辑vim /etc/sudoers,把用户增加进去

```
# User privilege specification
root	ALL=(ALL:ALL) ALL
test    ALL=(ALL:ALL) ALL
```

编辑完成后，退出时需要增加!,使用:wq!退出，因为是只读文件