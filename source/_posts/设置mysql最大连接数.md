---
title: 设置mysql最大连接数
date: 2018-05-21 13:31:42
tags:
 - mysql
---

为什么要修改连接数，是因为默认的连接不满足需求，在硬件条件允许下，修改最大连接数达到满足需求的目的

> 查看当前mysql最大连接数

```
mysql> show variables like 'max_connections';
+-----------------+-------+
| Variable_name   | Value |
+-----------------+-------+
| max_connections | 1000  |
+-----------------+-------+
1 row in set (0.31 sec)
```

> 修改当前mysql最大连接数

```
mysql> set global max_connections=1000;
```

> 但是以上的修改方式，在mysql服务重启后会失效

> 查看当前mysql连接

```
mysql> SHOW PROCESSLIST;
+-----+------+------------------+------+---------+------+----------+------------------+
| Id  | User | Host             | db   | Command | Time | State    | Info             |
+-----+------+------------------+------+---------+------+----------+------------------+
|  66 | root | localhost        | NULL | Query   |    0 | starting | SHOW PROCESSLIST |
| 331 | root | 172.17.0.1:54084 | csos | Sleep   |    9 |          | NULL             |
| 332 | root | 172.17.0.1:54086 | csos | Sleep   |    8 |          | NULL             |
| 333 | root | 172.17.0.1:54090 | csos | Sleep   |    9 |          | NULL             |
| 334 | root | 172.17.0.1:54098 | csos | Sleep   |    9 |          | NULL             |
| 335 | root | 172.17.0.1:54096 | csos | Sleep   |    9 |          | NULL             |
| 336 | root | 172.17.0.1:54092 | csos | Sleep   |    8 |          | NULL             |
| 337 | root | 172.17.0.1:54088 | csos | Sleep   |    9 |          | NULL             |
| 338 | root | 172.17.0.1:54094 | csos | Sleep   |    9 |          | NULL             |
| 339 | root | 172.17.0.1:54100 | csos | Sleep   |    9 |          | NULL             |
| 340 | root | 172.17.0.1:54116 | csos | Sleep   |    8 |          | NULL             |
| 341 | root | 172.17.0.1:54104 | csos | Sleep   |    8 |          | NULL             |
| 342 | root | 172.17.0.1:54120 | csos | Sleep   |    9 |          | NULL             |
| 343 | root | 172.17.0.1:54106 | csos | Sleep   |    9 |          | NULL             |
| 344 | root | 172.17.0.1:54122 | csos | Sleep   |    8 |          | NULL             |
| 345 | root | 172.17.0.1:54108 | csos | Sleep   |    8 |          | NULL             |
| 346 | root | 172.17.0.1:54110 | csos | Sleep   |    8 |          | NULL             |
| 347 | root | 172.17.0.1:54112 | csos | Sleep   |    9 |          | NULL             |
| 348 | root | 172.17.0.1:54102 | csos | Sleep   |    8 |          | NULL             |
| 349 | root | 172.17.0.1:54114 | csos | Sleep   |    8 |          | NULL             |
| 350 | root | 172.17.0.1:54118 | csos | Sleep   |    8 |          | NULL             |
+-----+------+------------------+------+---------+------+----------+------------------+
21 rows in set (0.00 sec)
```

> 如果查看连接数需要过滤的话，可以如下操作

```
mysql> select * from information_schema.PROCESSLIST where db='csos';
+-----+------+------------------+------+---------+------+-------+------+
| ID  | USER | HOST             | DB   | COMMAND | TIME | STATE | INFO |
+-----+------+------------------+------+---------+------+-------+------+
| 354 | root | 172.17.0.1:54130 | csos | Sleep   |    4 |       | NULL |
| 355 | root | 172.17.0.1:54132 | csos | Sleep   |    4 |       | NULL |
+-----+------+------------------+------+---------+------+-------+------+
2 rows in set (0.00 sec)
```