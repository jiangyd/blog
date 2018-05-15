---
title: '通过ssh config配置, 使git用不同私钥提交代码'
date: 2018-05-15 15:54:06
tags:
 - ssh
 - git
---

我有在用hexo部署blog在github上，本地构建完成后，直接可以把编译后的文件部署上去

但是，我希望源文件也可以存放在github上，所以我又搞了一个仓库地址

但是在当前目录提交代码时，只能提交一个，另一个提交不了，因为默认使用的私钥文件不对

为了区分开来，我只能配置不同的私钥文件,可以参考下我的配置文件



```
$ touch ~/.ssh/config
$ vim ~/.ssh/config

HOST server1
 HOSTNAME 192.168.56.102
 User jiangyd
 Port 3333

HOST gitblog.com
  HOSTNAME github.com
  IdentityFile ~/.ssh/id_rsa

HOST github.com
  HOSTNAME github.com
  IdentityFile ~/.ssh/github.com
```

参数|说明|
---|---
HOST | 别名    
HOSTNAME | 主机
User | 用户名
Port | 端口
IdentityFile | 私钥文件   



> 连接例子

```
$ ssh server1
jiangyd@192.168.56.102's password:
```

> 我的hexo _config.yml github仓库配置，注意这里用别名

```
deploy:
  type: git
  repo: git@gitblog.com:jiangyd/jiangyd.github.io
  branch: master
```

> 这样我就可以在hexo blog项目里面，即可以提交源代码到Github，又可以提交构建部署的代码到Github
因为部署的github与源代码仓库地址不是一个


以上的配置都是太过复杂，为什么会出现这种情况，是因为我把自已电脑的公钥存放到Github项目
里面了，而不同项目又不能存放同一个公钥，所以才会有我上面的这些设置

那么只要把公钥在Github上面做全局的设置，那么我本地就不用搞这么复杂了
