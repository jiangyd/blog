---
title: nginx配置https简单实例
date: 2018-05-31 07:59:25
tags:
 - nginx
 - ssl
 - https
---


1. 首先需要有域名，正好我有个域名testwd.cn
2. 给域名申请证书，可以找个免费的[https://freessl.org/](https://freessl.org/)，也可以找个花钱的
3. 阿里云ecs一台,需要有公网ip

> 首先给域名申请证书

{% asset_img freessl.org.jpg 申请证书示范 %}

> 把验证信息添加到DNS服务商解析记录

{% asset_img yanzhen.jpg 验证 %}
{% asset_img dns_auth.jpg 添加验证解析 %}

> 验证通过后，下载证书

{% asset_img crt_key.jpg 拷贝证书内容及私钥 %}

> nginx配置

把证书内容拷贝到/home/testwd.cn.crt;
把私钥内容拷贝到/home/testwd.cn.key

```
# cat /etc/nginx/conf.d/test.conf 
server {
  listen 443 ssl;
  ssl on;
  server_name web.testwd.cn;

  index index.html;
  root /home/dist/;

  ssl_certificate /home/testwd.cn.crt;
  ssl_certificate_key /home/testwd.cn.key;
 


}
```

> 查看本ecs实例公网ip

```
# curl ip.sb
47.96.86.120
```

> 配置DNS域名解析到ip

{% asset_img dns_ip.jpg 域名解析到公网ip %}


> 重新启动nginx

```
# /etc/init.d/nginx restart
[ ok ] Restarting nginx (via systemctl): nginx.service.
```

> 查看是否监听443端口

```
# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      836/sshd        
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      17588/nginx -g daem
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      17588/nginx -g daem
tcp6       0      0 :::80                   :::*                    LISTEN      17588/nginx -g daem
```

> 验证通过https访问

{% asset_img success.jpg 成功界面 %}