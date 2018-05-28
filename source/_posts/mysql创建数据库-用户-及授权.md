---
title: 'mysql创建数据库,用户,及授权'
date: 2018-05-28 09:50:50
tags:
 - mysql
---

> 创建数据库

```
mysql> create database testdb default character set utf8 default collate utf8_general_ci;
Query OK, 1 row affected (0.00 sec)
```

> 创建用户

```
mysql> create user 'jyd'@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.04 sec)
```

> 给用户授权

```
mysql> GRANT ALL PRIVILEGES ON testdb.* to 'jyd'@'%';
Query OK, 0 rows affected, 1 warning (0.00 sec)
```

> 更改用户密码

```
mysql> SET PASSWORD FOR 'jyd'@'%' = PASSWORD('new123456');
Query OK, 0 rows affected, 1 warning (0.00 sec)
```