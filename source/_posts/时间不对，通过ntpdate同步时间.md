---
title: 时间不对，通过ntpdate同步时间
date: 2018-05-21 12:47:44
tags: 
 - 时间同步
 - ntpdate
---


> 我有一台虚拟机，时间不对，影响我使用,使用阿里云公网NTP服务同步时间

```
# ntpdate ntp1.aliyun.com
21 May 12:50:42 ntpdate[6794]: step time server 182.92.12.11 offset 0.548418 sec
```

其它阿里云公网NTP时间服务器

公网NTP 
---
ntp2.aliyun.com 
ntp3.aliyun.com 
ntp4.aliyun.com 
ntp5.aliyun.com 
ntp6.aliyun.com
ntp7.aliyun.com