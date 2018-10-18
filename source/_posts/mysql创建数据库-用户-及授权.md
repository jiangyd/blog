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

> 也可以这样设置用户密码

```
mysql> update user set password= PASSWORD('123456') where user='root';
Query OK, 0 rows affected (0.00 sec)
Rows matched: 3  Changed: 0  Warnings: 0

mysql> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```


> 查看mysql版本号

##### 使用select version()命令

```
mysql> select version();
+-----------+
| version() |
+-----------+
| 5.1.73    |
+-----------+
1 row in set (0.00 sec)

```

##### 使用status命令

```
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.1.73, for redhat-linux-gnu (x86_64) using readline 5.1

Connection id:		121
Current database:	mysql
Current user:		root@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.1.73 Source distribution
Protocol version:	10
Connection:		127.0.0.1 via TCP/IP
Server characterset:	latin1
Db     characterset:	latin1
Client characterset:	latin1
Conn.  characterset:	latin1
TCP port:		3306
Uptime:			37 min 8 sec

Threads: 3  Questions: 9881  Slow queries: 0  Opens: 36  Flush tables: 1  Open tables: 28  Queries per second avg: 4.434
--------------
```

##### 刚进入mysql的时候

```
[root@iZm5egfy7v1z256fhrdir5Z csos]# mysql -u root -h 127.0.0.1 -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 122
Server version: 5.1.73 Source distribution

Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```