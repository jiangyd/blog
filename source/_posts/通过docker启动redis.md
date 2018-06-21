---
title: 通过docker启动redis
date: 2018-06-21 17:15:59
tags:
 - redis
---

> 通过docker启动redis,并设置认证密码

```
docker run --name some-redis -d redis --requirepass "123456"
```

```
# redis-cli
127.0.0.1:6379> select 1
(error) NOAUTH Authentication required.
127.0.0.1:6379> AUTH 123456
OK
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]>
```


> 通过docker启动redis,并把数据挂载到宿主机磁盘

docker run --name my-redis -v /Users/jiangyd/myredis:/data -d redis-cli

```
# redis-cli
127.0.0.1:6379> set name jyd
OK
127.0.0.1:6379> get name
"jyd"
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> set name lj
OK
127.0.0.1:6379[1]> get name
"lj"
127.0.0.1:6379[1]> exit
```

可以看的这个目录多了个文件

```
$ pwd
/Users/jiangyd/myredis
jiangyd:myredis jiangyd$ ls -l
total 8
-rw-r--r--@ 1 jiangyd  staff  122  6 21 17:26 dump.rdb
```