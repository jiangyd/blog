---
title: ubuntu apt使用阿里源
date: 2018-05-09 15:59:05
tags: 
 - apt
 - apt-get
---

#### ubuntu 14.04使用阿里的软件源

> 备份

```
# cp /etc/apt/sources.list /etc/apt/sources.list.bak
```

> 替换

```
cat >/etc/apt/sources.list<<EOF
deb https://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ trusty main restricted universe multiverse
deb https://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ trusty-security main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ trusty-updates main restricted universe multiverse

deb https://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse
deb-src https://mirrors.aliyun.com/ubuntu/ trusty-backports main restricted universe multiverse

## Not recommended
# deb https://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
# deb-src https://mirrors.aliyun.com/ubuntu/ trusty-proposed main restricted universe multiverse
EOF
```

> 最后执行apt更新

```
# apt-get update
```

> 其它系统参考此地址

[https://opsx.alibaba.com/mirror](https://opsx.alibaba.com/mirror)