---
title: 记一次crontab未生效原因
date: 2018-06-20 06:47:51
tags:
 - crontab
categories:
 - Linux
---



> 我有一个定时任务没有生效，看下我的定时任务脚本,当前用户是root

```
# cd /var/spool/cron/
# cat root 
*/1 * * * * sh /var/spool/cron/test.sh
```

 
检测脚本文件是否存在 (这里脚本要写绝对路径)

```
# ls /var/spool/cron/test.sh
/var/spool/cron/test.sh
```


检测脚本文件内容及是否能够执行

```
# cat test.sh 
#!/bin/bash

echo "a" >>b.txt
# sh /var/spool/cron/test.sh
# ls
b.txt  root  test.sh
[root@centos-7 cron]# cat b.txt 
a
```

检测cron服务是否运行中

```
# service crond status
Redirecting to /bin/systemctl status crond.service
● crond.service - Command Scheduler
   Loaded: loaded (/usr/lib/systemd/system/crond.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2018-03-22 20:44:34 CST; 2 months 28 days ago
 Main PID: 558 (crond)
   Memory: 2.3M
   CGroup: /system.slice/crond.service
           └─558 /usr/sbin/crond -n
```


检测一下crontab的日志,说明是有执行的

```
]# tailf /var/log/cron
Jun 20 07:00:01 centos-7 CROND[31232]: (root) CMD (/usr/lib64/sa/sa1 1 1)
Jun 20 07:00:01 centos-7 CROND[31231]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:01:01 centos-7 CROND[31252]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:01:01 centos-7 CROND[31253]: (root) CMD (run-parts /etc/cron.hourly)
Jun 20 07:01:01 centos-7 run-parts(/etc/cron.hourly)[31253]: starting 0anacron
Jun 20 07:01:01 centos-7 run-parts(/etc/cron.hourly)[31263]: finished 0anacron
Jun 20 07:02:01 centos-7 CROND[31307]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:03:01 centos-7 CROND[31367]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:04:01 centos-7 CROND[31401]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:05:01 centos-7 CROND[31406]: (root) CMD (sh /var/spool/cron/test.sh)
Jun 20 07:06:01 centos-7 CROND[31426]: (root) CMD (sh /var/spool/cron/test.sh)
```


> 这样定位肯定是脚本文件有问题了,把b.txt的路径改成绝对路径

```
# cat test.sh 
#!/bin/bash

echo "a" >>/root/b.txt
# cat /root/b.txt
a
```

果然可以执行了