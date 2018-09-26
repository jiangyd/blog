---
title: 'mysql创建数据库,用户,及授权'
date: 2018-05-28 09:50:50
tags:
 - mysql
---

> 创建数据库,注意数据库加上反撇号,不然有些带横杠的会被认为是减号，如test-de会创建不成功

> 加上反撇号的还有一层用意,使用mysql中的关键字命名不会报错

```
mysql> create database create;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'create' at line 1
mysql> create database crtest;
Query OK, 1 row affected (0.00 sec)

mysql> create database `create`;
Query OK, 1 row affected (0.00 sec)
```


```
mysql> create database  `testdb` default character set utf8 default collate utf8_general_ci;
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
> 给某数据库设置只读权限（message_bb）

```
mysql> GRANT SELECT ON message_bb.* to 'jyd'@'%';
Query OK, 0 rows affected (0.00 sec)
```

> 刷新权限

```
mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

> 更改用户密码

```
mysql> SET PASSWORD FOR 'jyd'@'%' = PASSWORD('new123456');
Query OK, 0 rows affected, 1 warning (0.00 sec)
```