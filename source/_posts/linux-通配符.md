---
title: linux 通配符
date: 2018-11-06 17:27:24
tags:
	- linux
---

准备测试数据

```
# touch a.txt a1.txt a2.txt a3.txt ab.txt abc.txt ad.txt
# ls
a1.txt  a2.txt  a3.txt  abc.txt  ab.txt  ad.txt  a.txt
```


### ?

匹配任意一个字符,只匹配一个

```
# ls a?.txt
a1.txt  a2.txt  a3.txt  ab.txt  ad.txt
```


### *

 匹配0个或任意多个，也就是匹配任意内容

```
# ls a*.txt
a1.txt  a2.txt  a3.txt  abc.txt  ab.txt  ad.txt  a.txt
```

### []

匹配括号中的任意一个，例如[abc]可以匹配a,或者是b,或者是c

```
# ls a[abd].txt
ab.txt  ad.txt
```


### [-]

匹配一个范围，例如[0-9]匹配数字0到9,[a-z]匹配任意一个小写字母

```
# ls a[0-9].txt
a1.txt  a2.txt  a3.txt

# ls a[a-z].txt
ab.txt  ad.txt
```

### [^]

逻辑非，匹配括号里面以外的内容，例如[^0-9],匹配除了0到数字9之外的内容

```
# ls a[^0-9].txt
ab.txt  ad.txt


# ls a[^a-z].txt
a1.txt  a2.txt  a3.txt
```