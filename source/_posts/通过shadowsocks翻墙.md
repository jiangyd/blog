---
title: 通过shadowsocks翻墙
date: 2018-11-03 07:20:56
tags:
	- shadowsocks
---


公司内部有免费提供shadowsocks服务，可以通过它翻墙，它是socks5协议代理

官方网址: https://shadowsocks.org

## 服务端

今天特地自己试了下

1. 先通过阿里云购买国外地域的ecs,需要公网带宽 (我购买的地域是: 美国硅谷)

2. 安装（本系统是ubuntu16.04）


```
# pip install shadowsocks

```

在线帮助已经在github上删除了

```
# ssserver --help
usage: ssserver [OPTION]...
A fast tunnel proxy that helps you bypass firewalls.

You can supply configurations via either config file or command line arguments.

Proxy options:
  -c CONFIG              path to config file
  -s SERVER_ADDR         server address, default: 0.0.0.0
  -p SERVER_PORT         server port, default: 8388
  -k PASSWORD            password
  -m METHOD              encryption method, default: aes-256-cfb
  -t TIMEOUT             timeout in seconds, default: 300
  --fast-open            use TCP_FASTOPEN, requires Linux 3.7+
  --workers WORKERS      number of workers, available on Unix/Linux
  --forbidden-ip IPLIST  comma seperated IP list forbidden to connect
  --manager-address ADDR optional server manager UDP address, see wiki

General options:
  -h, --help             show this help message and exit
  -d start/stop/restart  daemon mode
  --pid-file PID_FILE    pid file for daemon mode
  --log-file LOG_FILE    log file for daemon mode
  --user USER            username to run as
  -v, -vv                verbose mode
  -q, -qq                quiet mode, only show warnings/errors
  --version              show version information

Online help: <https://github.com/shadowsocks/shadowsocks>
```

3. 配置(多用户与单用户，二选一)

	多用户 (多个端口与密码,每个用户使用不同端口与密码连接)

```
# vim /etc/shadowsocks.json

{
    "server":"0.0.0.0",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
	    "50001":"jiangyongdao",
	    "50002":"jiangyd",
	    "60001":"123456"
	},
    "timeout":3000,
    "method":"rc4-md5",
    "fast_open":false

}

```


   单用户


```
{
    "server":"0.0.0.0",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"jiangyd",
    "timeout":300,
    "method":"rc4-md5"
}
```


4. 启动


```
# ssserver -c /etc/shadowsocks.json --log-file /var/log/shadowsocks.log -d start
INFO: loading config from /etc/shadowsocks.json
2018-11-03 07:17:49 WARNING  warning: your timeout 3000 seems too long
2018-11-03 07:17:50 INFO     loading libcrypto from libcrypto.so.1.0.0
started

# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      801/sshd        
tcp        0      0 0.0.0.0:60001           0.0.0.0:*               LISTEN      1045/python     
tcp        0      0 0.0.0.0:50001           0.0.0.0:*               LISTEN      1045/python     
tcp        0      0 0.0.0.0:50002           0.0.0.0:*               LISTEN      1045/python     
```

5. 配置系统自启动


```
# vim /lib/systemd/system/shadowsocks.service

[Unit]
Description=Shadowsocks Server
After=network.target

[Service]
ExecStart=/usr/local/bin/ssserver -c /etc/shadowsocks.json --log-file /var/log/shadowsocks.log 
Restart=on-abort

[Install]
WantedBy=multi-user.target

# systemctl start shadowsocks.service
# systemctl enable shadowsocks.service

# systemctl status shadowsocks.service
● shadowsocks.service - Shadowsocks Server
   Loaded: loaded (/lib/systemd/system/shadowsocks.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2018-11-03 08:28:52 CST; 4s ago
 Main PID: 5481 (ssserver)
   CGroup: /system.slice/shadowsocks.service
           └─5481 /usr/bin/python /usr/local/bin/ssserver -c /etc/shadowsocks.json --log-file /var/log/shadowsocks.log
```



## 客户端使用

使用命令行客户端,也可以下载gui客户端

使用ubuntu系统

```
#pip install shadowsocks

```

配置

```
#vim /etc/shadowsocks.json

{
    "server":"47.254.17.64",
    "server_port":8388,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"jiangyd",
    "timeout":600,
    "method":"rc4-md5"
}

```

启动（客户端启动是sslocal）

```
sslocal -c /etc/shadowsocks.json --log-file /var/log/shadowsocks.log -d start
```

由于shadowsocks是socks5代理,一些只能通过http代理的软件就无法翻墙（例如:wget），可以把socks5转成http代理

安装privoxy (其它类似的软件还有polipo)

```
apt update
apt install privoxy
```

配置 (privoxy默认监听端口8118，也可以改成其它端口)

```
vim /etc/privoxy/config

#listen-address  localhost:8118
forward-socks5 / 127.0.0.1:1080 .
listen-address 0.0.0.0:1088

```

重启

```
/etc/init.d/privoxy restart

# netstat -lntp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      925/sshd        
tcp        0      0 127.0.0.1:1080          0.0.0.0:*               LISTEN      1234/python     
tcp        0      0 0.0.0.0:1088            0.0.0.0:*               LISTEN      16596/privoxy   
```

设置临时环境变量,也可以设置永久环境变量

```
#设置临时环境变量，系统重启就不生效
export http_proxy='http://127.0.0.1:1088'
export https_proxy='http://127.0.0.1:1088'

#设置永久环境变量

# vim ~/.bashrc
export http_proxy='http://127.0.0.1:1088'
export https_proxy='http://127.0.0.1:1088'

```

通过wget测试,状态返回200

```
# wget https://www.google.com
--2018-11-03 12:14:53--  https://www.google.com/
Connecting to 127.0.0.1:1088... connected.
Proxy request sent, awaiting response... 302 Found
Location: https://www.google.com.hk/url?sa=p&hl=zh-CN&pref=hkredirect&pval=yes&q=https://www.google.com.hk/%3Fgws_rd%3Dcr&ust=1541218524162810&usg=AOvVaw0i_cfp2MIeaJUoukSe2Mmx [following]
--2018-11-03 12:14:54--  https://www.google.com.hk/url?sa=p&hl=zh-CN&pref=hkredirect&pval=yes&q=https://www.google.com.hk/%3Fgws_rd%3Dcr&ust=1541218524162810&usg=AOvVaw0i_cfp2MIeaJUoukSe2Mmx
Connecting to 127.0.0.1:1088... connected.
Proxy request sent, awaiting response... 302 Found
Location: https://www.google.com.hk/?gws_rd=cr [following]
--2018-11-03 12:14:54--  https://www.google.com.hk/?gws_rd=cr
Reusing existing connection to www.google.com.hk:443.
Proxy request sent, awaiting response... 200 OK
Length: unspecified [text/html]
Saving to: ‘index.html.3’

index.html.3                               [ <=>                                                                         ]  10.88K  --.-KB/s    in 0.004s  

2018-11-03 12:14:55 (2.92 MB/s) - ‘index.html.3’ saved [11146]

```


通过curl测试，可以发现出口ip，正是shadowsocks服务器的ip
```
# curl ip.sb
47.254.17.64
```