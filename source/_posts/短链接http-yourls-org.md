---
title: '短链接http://yourls.org/'
date: 2018-12-07 17:28:11
tags:
    - 短链接
    - yourls
---

有些网址特别长，不容易记住，而且有些地方还会限制内容长度，例如：短信，如果你想把这个链接推广给你的客户，而网址又太长的话，客户那边可能就收到两条短信了，http://yourls.org/ 就是一个开源的短链接生成框架，使用php开发的，我公司正好用到了，顺便记录下笔记


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

安装数据库
```
# apt-get update
# apt-get install -y mariadb-server
```

设置数据库
```
MariaDB [(none)]> CREATE DATABASE yourls;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> create user 'jyd'@'%' IDENTIFIED BY '123456';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON yourls.* to 'jyd'@'%';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

安装php

官网中这一步执行报错
```
# add-apt-repository -y ppa:ondrej/php-7.0
Cannot add PPA: 'ppa:ondrej/php-7.0'.
Please check that the PPA name or format is correct.
```

可更改如下

```
# add-apt-repository -y ppa:ondrej/php
gpg: keyring `/tmp/tmp4iq4rdr1/secring.gpg' created
gpg: keyring `/tmp/tmp4iq4rdr1/pubring.gpg' created
gpg: requesting key E5267A6C from hkp server keyserver.ubuntu.com
gpg: /tmp/tmp4iq4rdr1/trustdb.gpg: trustdb created
gpg: key E5267A6C: public key "Launchpad PPA for Ondřej Surý" imported
gpg: Total number processed: 1
gpg:               imported: 1  (RSA: 1)
OK
```

```
# apt-get update
# apt-get -y install php7.0-fpm php7.0-cli php7.0-mysql php7.0-curl
```

安装nginx

```
# apt-get -y install nginx 
```


git clone yourls

```
# mkdir /var/www
# cd /var/www/
# git clone --branch master https://github.com/YOURLS/YOURLS.git yourls
# ls
yourls
```

配置yourls

```
# cd /var/www/yourls/user/
# ls
config-sample.php  index.html  languages  plugins
# cp config-sample.php config.php
# vim config.php
<?php
/* This is a sample config file.
 * Edit this file with your own settings and save it as "config.php"
 *
 * IMPORTANT: edit and save this file as plain ASCII text, using a text editor, for instance TextEdit on Mac OS or
 * Notepad on Windows. Make sure there is no character before the opening <?php at the beginning of this file.
 */

/*
 ** MySQL settings - You can get this info from your web host
 */

/** MySQL database username */
define( 'YOURLS_DB_USER', 'jyd' );

/** MySQL database password */
define( 'YOURLS_DB_PASS', '123456' );

/** The name of the database for YOURLS */
define( 'YOURLS_DB_NAME', 'yourls' );

/** MySQL hostname.
 ** If using a non standard port, specify it like 'hostname:port', eg. 'localhost:9999' or '127.0.0.1:666' */
define( 'YOURLS_DB_HOST', 'localhost' );

/** MySQL tables prefix */
define( 'YOURLS_DB_PREFIX', 'yourls_' );

/*
 ** Site options
 */

/** YOURLS installation URL -- all lowercase, no trailing slash at the end.
 ** If you define it to "http://sho.rt", don't use "http://www.sho.rt" in your browser (and vice-versa) */
define( 'YOURLS_SITE', 'http://47.110.136.175' );

/** Server timezone GMT offset */
define( 'YOURLS_HOURS_OFFSET', 0 ); 

/** YOURLS language
 ** Change this setting to use a translation file for your language, instead of the default English.
 ** That translation file (a .mo file) must be installed in the user/language directory.
 ** See http://yourls.org/translations for more information */
define( 'YOURLS_LANG', '' ); 

/** Allow multiple short URLs for a same long URL
 ** Set to true to have only one pair of shortURL/longURL (default YOURLS behavior)
 ** Set to false to allow multiple short URLs pointing to the same long URL (bit.ly behavior) */
define( 'YOURLS_UNIQUE_URLS', true );

/** Private means the Admin area will be protected with login/pass as defined below.
 ** Set to false for public usage (eg on a restricted intranet or for test setups)
 ** Read http://yourls.org/privatepublic for more details if you're unsure */
define( 'YOURLS_PRIVATE', true );

/** A random secret hash used to encrypt cookies. You don't have to remember it, make it long and complicated. Hint: copy from http://yourls.org/cookie **/
define( 'YOURLS_COOKIEKEY', 'dfghjwehjlksfdjklkdjfjhgmdskfnshjkdhg~!@#$%^&*' );

/** Username(s) and password(s) allowed to access the site. Passwords either in plain text or as encrypted hashes
 ** YOURLS will auto encrypt plain text passwords in this file
 ** Read http://yourls.org/userpassword for more information */
$yourls_user_passwords = array(
	'admin' => 'qwe123123',
	// 'username2' => 'password2',
	// You can have one or more 'login'=>'password' lines
	);

/** Debug mode to output some internal information
 ** Default is false for live site. Enable when coding or before submitting a new issue */
define( 'YOURLS_DEBUG', false );
	
/*
 ** URL Shortening settings
 */

/** URL shortening method: 36 or 62 */
define( 'YOURLS_URL_CONVERT', 36 );
/*
 * 36: generates all lowercase keywords (ie: 13jkm)
 * 62: generates mixed case keywords (ie: 13jKm or 13JKm)
 * Stick to one setting. It's best not to change after you've started creating links.
 */

/** 
* Reserved keywords (so that generated URLs won't match them)
* Define here negative, unwanted or potentially misleading keywords.
*/
$yourls_reserved_URL = array(
	'porn', 'faggot', 'sex', 'nigger', 'fuck', 'cunt', 'dick',
);

/*
 ** Personal settings would go after here.
 */

```



配置nginx

```
cd /etc/nginx/sites-available/
# ls
default
# rm -rf default 
# cd ../conf.d/
# vim yourls.conf
server {
    listen      80;
    server_name 47.110.136.175;
    root        /var/www/yourls;

    access_log /var/www/yourls/logs/access.log;
    error_log /var/www/yourls/logs/error.log;

    index index.php;

    location / {
        try_files $uri $uri/ /yourls-loader.php;
        expires 14d;
        add_header Cache-Control 'public';
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_intercept_errors off;
        fastcgi_buffer_size 16k;
        fastcgi_buffers 4 16k;
    }
}
```

创建日志目录

```
# mkdir /var/www/yourls/logs
```

重启nginx

```
/etc/init.d/nginx restart
```

初始化访问
{% asset_img install.jpg  %}

安装
{% asset_img install2.jpg 安装 %}

登录，用户名及密码就是自定义在配置文件config.php
{% asset_img login.jpg 登录 %}


查看api使用，及token
{% asset_img api.jpg api %}


通过python调用

```
import requests


r=requests.get("http://47.110.136.175/yourls-api.php?username=admin&password=qwe123123&action=shorturl&format=json&url=http://www.qq.com")
print(r.json())


{'url': {'keyword': '1', 'url': 'http://www.qq.com', 'title': '腾讯首页', 'date': '2018-12-08 01:52:59', 'ip': '220.112.127.23'}, 'status': 'success', 'message': 'http://www.qq.com added to database', 'title': '腾讯首页', 'shorturl': 'http://47.110.136.175/1', 'statusCode': 200}
```

效果就是: 访问http://47.110.136.175/1 就等于访问http://www.qq.com


上面的请求，用户名与密码都在参数中，比较不安全，接下来使用签名访问

```
import requests
import hashlib
import time

t=str(int(time.time()))
token='e733cc53c8'
m=hashlib.md5()

data=t+token
m.update(data.encode("utf-8"))

signature=m.hexdigest()

r=requests.get("http://47.110.136.175/yourls-api.php?timestamp="+t+"&signature="+signature+"&password=qwe123123&action=shorturl&format=json&url=http://www.baidu.com")
print(r.json())


{'url': {'keyword': '2', 'url': 'http://www.baidu.com', 'title': '百度一下，你就知道', 'date': '2018-12-08 02:00:39', 'ip': '220.112.127.23'}, 'status': 'success', 'message': 'http://www.baidu.com added to database', 'title': '百度一下，你就知道', 'shorturl': 'http://47.110.136.175/2', 'statusCode': 200}
```

效果就是: 访问http://47.110.136.175/2 就等于访问http://www.baidu.com



可以看出，返回的短链接，都是后面带数字的，看起来不是很安全，容易被遍历访问


使用yourls插件,返回随机字符串

https://github.com/YOURLS/random-keywords

从github上把plugin.php文件拷贝到下面创建的目录

```
# cd /var/www/yourls/user/plugins/
# mkdir random-keywords
# cd random-keywords/
```

{% asset_img plugin.jpg 激活插件 %}


再次访问

```
import requests
import hashlib
import time

t=str(int(time.time()))
token='e733cc53c8'
m=hashlib.md5()

data=t+token
m.update(data.encode("utf-8"))

signature=m.hexdigest()

r=requests.get("http://47.110.136.175/yourls-api.php?timestamp="+t+"&signature="+signature+"&password=qwe123123&action=shorturl&format=json&url=http://www.163.com")
print(r.json())


返回短链接为 http://47.110.136.175/8be0r

{'url': {'keyword': '8be0r', 'url': 'http://www.163.com', 'title': '网易', 'date': '2018-12-08 02:12:42', 'ip': '220.112.127.23'}, 'status': 'success', 'message': 'http://www.163.com added to database', 'title': '网易', 'shorturl': 'http://47.110.136.175/8be0r', 'statusCode': 200}
```


管理页面

{% asset_img home.jpg 管理 %}