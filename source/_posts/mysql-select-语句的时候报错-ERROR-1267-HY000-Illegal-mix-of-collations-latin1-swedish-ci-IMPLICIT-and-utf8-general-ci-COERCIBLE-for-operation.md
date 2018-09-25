---
title: >-
  mysql select 语句的时候报错: ERROR 1267 (HY000): Illegal mix of collations
  (latin1_swedish_ci,IMPLICIT) and (utf8_general_ci,COERCIBLE) for operation '='
date: 2018-09-25 13:31:37
tags:
	- mysql
---

> 一个简单的sql查询语句竟然还报错

```
mysql> insert into jj values ('端口');
Query OK, 1 row affected, 1 warning (0.02 sec)

mysql> select * from jj;
+------+
| name |
+------+
| ??   |
+------+
1 row in set (0.00 sec)

mysql> select * from jj where name='端口';
ERROR 1267 (HY000): Illegal mix of collations (latin1_swedish_ci,IMPLICIT) and (utf8_general_ci,COERCIBLE) for operation '='
```

> 以上错误是第一次见，但估计与字符编码有关系,查看表结构发现字典name的编码为latin1

```
mysql> show create table jj;
+-------+----------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                 |
+-------+----------------------------------------------------------------------------------------------+
| jj    | CREATE TABLE `jj` (
  `name` varchar(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1 |
+-------+----------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```

> 我们把这个字段的编码改一改,这样编码一致问题就解决了

```
mysql> alter table jj change  `name` `name`  varchar(100) character set utf8 ;
Query OK, 1 row affected (0.05 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> select * from jj where name='端口';
Empty set (0.00 sec)

mysql> insert into jj values ('端口');
Query OK, 1 row affected (0.01 sec)

mysql> select * from jj where name='端口';
+--------+
| name   |
+--------+
| 端口   |
+--------+
1 row in set (0.00 sec)

```

> 问题虽然解决了，但是为什么会出现此问题，我们先尝试创建一个新表，查看下字符编码,发现编码也是
latin1，再次查看数据库(csos)的编码,应该不难发现源头就在这里

```
mysql> create table cc (name varchar(20));
Query OK, 0 rows affected (0.02 sec)

mysql> show create table cc;
+-------+----------------------------------------------------------------------------------------------+
| Table | Create Table                                                                                 |
+-------+----------------------------------------------------------------------------------------------+
| cc    | CREATE TABLE `cc` (
  `name` varchar(20) DEFAULT NULL
) ENGINE=InnoDB DEFAULT CHARSET=latin1 |
+-------+----------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

mysql> SHOW CREATE DATABASE csos;
+----------+-----------------------------------------------------------------+
| Database | Create Database                                                 |
+----------+-----------------------------------------------------------------+
| csos     | CREATE DATABASE `csos` /*!40100 DEFAULT CHARACTER SET latin1 */ |
+----------+-----------------------------------------------------------------+
1 row in set (0.00 sec)
```
