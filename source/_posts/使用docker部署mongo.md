---
title: 使用docker部署mongo
date: 2018-10-15 12:38:23
tags:
	- docker
	- mongo
categories: "mongo入门学习笔记"	
---

以前只知道有mongo，但是没有用过，正好在公司负责测试的项目有部分数据是存到mongo里面的，那些数据呢，是一些登陆日志，纪录登陆用户名，帐号，时间，还有一些其它类型的日志，我很奇怪这些数据，明明存数据库就是可以，为什么偏要存mongo里面，所以请教了负责这个项目的开发，他给我的回复是，业务要求这些数据是历史数据，就是当用户名称发生改变后，以前的那些登陆日志用户名并不会更改，以前叫什么名字就是什么名字，也就是说这些数据虽然包含了很多用户表里面的字段，但是与用户表并没有关联关系，存数据库可以，但却是冗余数据，而mongo呢是非关系数据库，所以把它存在mongo里面，查询速度又快



 > 虽然我作为测试，但是萌生了学习下mongo的念头，正好公司部署mongo的方式是使用docker，所以我先从docker部署开始一些简单入门的学习



1. mongo官方docker镜像地址

 https://hub.docker.com/_/mongo/

2. 运行docker镜像，设置超级管理账户及密码,数据目录挂载，端口

```
$ docker run -d --name some-mongo -v /Users/jiangyd/mymongodata:/data/db -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=jyd -e MONGO_INITDB_ROOT_PASSWORD=123456 mongo
```

3. 进入容器，进行连接

```
jiangyd:~ jiangyd$ docker ps |grep mongo
2337d1ef0c8e        mongo               "docker-entrypoint.s…"   4 minutes ago       Up 4 minutes        0.0.0.0:27017->27017/tcp   some-mongo
jiangyd:~ jiangyd$ docker exec -it 2337d1ef0c8e /bin/bash
root@2337d1ef0c8e:/# mongo -u jyd -p 123456 --authenticationDatabase admin
MongoDB shell version v4.0.3
connecting to: mongodb://127.0.0.1:27017
Implicit session: session { "id" : UUID("21e24b40-2c8b-42ba-be06-73a4988e6838") }
MongoDB server version: 4.0.3
Server has startup warnings:
2018-10-15T07:32:39.628+0000 W CONTROL  [initandlisten]
2018-10-15T07:32:39.628+0000 W CONTROL  [initandlisten]
2018-10-15T07:32:39.628+0000 I CONTROL  [initandlisten]
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---

>
```

4. mongo 默认有两个数据库，一个admin，一个local