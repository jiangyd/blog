---
title: '短链接http://yourls.org/'
date: 2018-12-07 17:28:11
tags:
    - 短链接
    - yourls
---

有些网址特别长，不容易记住，而且有些地方还是限制内容长度，例如：短信，如果你想把这个链接推广给你的客户，而网址又太长的话，客户那边可能就收到两条短信了，http://yourls.org/ 就是一个开源的短链接生成框架，使用php开发的，我公司正好用到了，顺便记录下笔记


1. 官网地址

http://yourls.org/


2. 参考在ubuntu14.04上安装

https://www.rosehosting.com/blog/how-to-install-yourls-on-ubuntu-14-04/


```
# apt-get update
# apt-get install software-properties-common git
# apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xcbcb082a1bb943db
# add-apt-repository 'deb http://ftp.osuosl.org/pub/mariadb/repo/10.1/ubuntu trusty main'
```

```
# apt-get update
# apt-get install -y mariadb-server
```


