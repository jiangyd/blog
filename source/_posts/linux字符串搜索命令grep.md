---
title: linux字符串搜索命令grep
date: 2018-10-30 07:12:01
tags:
	- linux
---

grep 搜索文件中的字符串,我通常有这样一个需求查找，就是知道字符串内容，但是不知道在那个文件中，那么可以通过-r来遍历目录查找了

```
# grep -r "test" ./
./jjc/te.txt:name=test
./jjc/a/a.txt:user=test
./jjb/ab.txt:name=test
./jjb/ab.txt:user=test
./jjb/b/cc.txt:name\test
./jjb/b/cc.txt:like test
```


通常在一个配置文件中，有很多注释，那么我想过滤这些内容怎么办

如以下文件,内容以#开头，;开头的我想去掉再显示处理，那么使用参数-v 取反，排除以#,;开头的数据

```
# cat ser.conf 
#this is demo
;port 1024
host 127.0.0.1
;uname
log status.log

# grep -v "^[#;]" ser.conf 
host 127.0.0.1
log status.log
```

如果还要排除空行，注释呢

-E ^$(正则中表示空) 

```
grep -Ev "^$|^[#;]" ser.conf
```