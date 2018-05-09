---
title: docker cp
date: 2018-05-06 19:22:20
tags: 
 - docker
 - cp
categories: "docker常用命令"
---

### docker cp 使用记录

把容器里面的文件，目录拷贝到宿主机

```
$docker cp 2f4f188c5d1c:/etc/nginx/conf.d/testing.www.cloudcare.cn.conf nginx_conf/

```

```
$docker cp 2f4f188c5d1c:/etc/nginx/conf.d/ nginx_conf/
```


