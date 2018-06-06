---
title: docker build镜像，运行镜像出现问题
date: 2018-06-06 11:22:23
tags:
 - docker
---

> 我的系统环境是mac ,docker环境如下

```
$ docker version
Client:
 Version:	18.03.0-ce
 API version:	1.37
 Go version:	go1.9.4
 Git commit:	0520e24
 Built:	Wed Mar 21 23:06:22 2018
 OS/Arch:	darwin/amd64
 Experimental:	false
 Orchestrator:	swarm

Server:
 Engine:
  Version:	18.03.0-ce
  API version:	1.37 (minimum version 1.12)
  Go version:	go1.9.4
  Git commit:	0520e24
  Built:	Wed Mar 21 23:14:32 2018
  OS/Arch:	linux/amd64
  Experimental:	true
```


> docker build镜像，运行镜像出现问题 （为什么会出现这个问题，估计是我的空间满了，然后我删除了很多镜像，容器，不知道什么情况下就引起了这个问题）

```
$ docker build -t ubuntu:test . --no-cache
Sending build context to Docker daemon  189.5MB
Step 1/16 : FROM ubuntu:16.04
 ---> f975c5035748
Step 2/16 : RUN apt-get update
error creating overlay mount to /var/lib/docker/overlay2/8861e6916ec6a1d012e9b1c2f73107f5e28e552da5f367d2ae02d0a359aa5c71-init/merged: no such file or directory
```

> 报错说没有文件或目录，我查看了下，/var/lib／docker这个目录是不存在的


在github上找到了解决办法，但是这个解决办法是不太理想的，因为我的镜像容器通通被删除了

[https://github.com/docker/for-mac/issues/1396](https://github.com/docker/for-mac/issues/1396)


解决办法:
1. #通过screen命令进入docker虚拟机
Screen into the Docker VM

2. #取消挂载，然后删除/var/lib/docker
Remove /var/lib/docker

3. #最后重启docker
Restart Docker via the widget

```
$ screen ~/Library/Containers/com.docker.docker/Data/com.docker.driver.amd64-linux/tty
$ umount /var/lib/docker/overlay2
$ rm -rf /var/lib/docker
```
