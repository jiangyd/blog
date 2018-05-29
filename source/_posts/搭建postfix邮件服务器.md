---
title: 搭建postfix邮件服务器
date: 2018-05-29 13:59:59
tags:
 - postfix
---

> 设置主机名为test.cn

```
$ sudo vim /etc/hostname
```
> 查看主机名设置是否生效

```
$ hostname
test.cn
```

> 更新及安装

```
$ sudo apt-get update
$ sudo apt-get install postfix
```

{% asset_img mail_configuration.png 选择Internet Site %}
{% asset_img mail_name.png 设置mail name %}

> 安装完成后监听端口

```
$ netstat -lntp|grep 25
(No info could be read for "-p": geteuid()=1000 but you should be root.)
tcp        0      0 0.0.0.0:25              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::25                   :::*                    LISTEN      -
```

> 安装mail测试下

```
$ sudo apt-get install mailutils
$ echo "this is a test email" |mail "962584902@qq.com"
```

> 看看日志是否正常

```
$ tailf /var/log/mail.log
May 29 14:14:32 test postfix/pickup[2416]: E275A601B5: uid=1000 from=<jiangyd@test.cn>
May 29 14:14:32 test postfix/cleanup[2938]: E275A601B5: message-id=<20180529061432.E275A601B5@test.cn>
May 29 14:14:32 test postfix/qmgr[2417]: E275A601B5: from=<jiangyd@test.cn>, size=320, nrcpt=1 (queue active)
May 29 14:14:33 test postfix/smtp[2940]: connect to mx3.qq.com[240e:ff:f040:28::f]:25: Network is unreachable
May 29 14:14:34 test postfix/smtp[2940]: E275A601B5: to=<962584902@qq.com>, relay=mx3.qq.com[183.57.48.35]:25, delay=1.4, delays=0.02/0.01/0.17/1.2, dsn=2.0.0, status=sent (250 Ok: queued as )
May 29 14:14:34 test postfix/qmgr[2417]: E275A601B5: removed
```

> 去qq邮箱看看

{% asset_img mail_to_qq.png 验证 %}
