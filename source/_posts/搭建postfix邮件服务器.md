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


> 试试其它机器，连接邮件服务器来发邮件，并用python实现

其它机器要能访问test.cn这个地址，可以本地绑定host

> python脚本

```
# coding=utf-8
__author__ = 'jyd'

import smtplib

from email.mime.text import MIMEText

smtpserver = "test.cn"
username = "jiangyd@test.cn"

msg = MIMEText("你好，这是一封测试邮件")
msg['subject'] = "测试邮件"
msg['From'] = "jiangyd@test.cn"
msg['To'] = "962584902@qq.com"

smtp = smtplib.SMTP()
smtp.connect(smtpserver)
smtp.sendmail(msg['From'], msg['To'], msg.as_string())
smtp.quit()

```

> 运行以上脚本报错了

```
    raise SMTPRecipientsRefused(senderrs)
smtplib.SMTPRecipientsRefused: {'962584902@qq.com': (454, b'4.7.1 <962584902@qq.com>: Relay access denied')}
```

> 看看日志，发现是访问被拒绝了,不允许这个192.168.56.1
```
$ tailf /var/log/mail.log
May 29 14:26:33 test postfix/smtpd[3171]: connect from unknown[192.168.56.1]
May 29 14:26:33 test postfix/smtpd[3171]: NOQUEUE: reject: RCPT from unknown[192.168.56.1]: 454 4.7.1 <962584902@qq.com>: Relay access denied; from=<jiangyd@test.cn> to=<962584902@qq.com> proto=ESMTP helo=<jiangyd.local>
May 29 14:26:33 test postfix/smtpd[3171]: lost connection after RSET from unknown[192.168.56.1]
May 29 14:26:33 test postfix/smtpd[3171]: disconnect from unknown[192.168.56.1]
```

> 更改设置 增加可信客户端网段192.168.56.0/24,也可是增加万能的0.0.0.0/0

```
$ sudo vim /etc/postfix/main.cf
mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128 192.168.56.0/24
```

> 更改后需要重启
```
$ sudo /etc/init.d/postfix restart
sudo: unable to resolve host test.cn
 * Stopping Postfix Mail Transport Agent postfix                                     [ OK ]
 * Starting Postfix Mail Transport Agent postfix                                     [ OK ]
```

> 再次验证
{% asset_img mail_to_qq_python.png 设置mail name %}

