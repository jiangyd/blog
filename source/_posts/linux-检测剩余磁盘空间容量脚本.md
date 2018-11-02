---
title: linux 检测剩余磁盘空间容量脚本
date: 2018-11-02 15:12:59
tags:
	- linux
---


看到开发写的一个脚本，觉得思路不错，记一下

他这个脚本就是检测剩余磁盘空间容量的


```
＃通过df查看磁盘使用情况，默认单位kb
root@iZbp1fczkfsaufav1m0wuiZ:~# df
Filesystem     1K-blocks    Used Available Use% Mounted on
udev              489520       0    489520   0% /dev
tmpfs             101596    2728     98868   3% /run
/dev/vda1       41151808 2664048  36374332   7% /
tmpfs             507964       0    507964   0% /dev/shm
tmpfs               5120       0      5120   0% /run/lock
tmpfs             507964       0    507964   0% /sys/fs/cgroup
tmpfs             101596       0    101596   0% /run/user/0


#通过grep 正则匹配以/结尾的数据
root@iZbp1fczkfsaufav1m0wuiZ:~# df |grep '/$'
/dev/vda1       41151808 2664048  36374332   7% /


＃通过awk 获取剩余磁盘空间容量所在第几列
root@iZbp1fczkfsaufav1m0wuiZ:~# df |grep '/$' |awk '{print $4}'
36374332
```