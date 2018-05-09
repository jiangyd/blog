---
title: docker logs
date: 2018-05-06 17:13:59
tags: docker
categories: "docker常用命令"
---

### docker logs 使用记录

最近常用到容器的这个命令

```
$ docker logs --help

Usage:	docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --help           Print usage
      --since string   Show logs since timestamp
      --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
```


实时查看日志
```
 $ docker logs -f a13d7f1818d6
```

查看日志产生的时间

```
$ docker logs -t  a13d7f1818d6
2018-05-06T08:53:26.689471000Z INFO:callme.server:Server with id='cloudcare' started.
```

查看最后多少条日志

```
docker logs -f --tail=10 a13d7f1818d6
```






