---
title: 设置mysql最大连接数
date: 2018-05-21 13:31:42
tags:
 － mysql
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
