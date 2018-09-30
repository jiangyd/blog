---
title: chrome 控制台某请求没有显示响应内容
date: 2018-09-26 20:14:31
tags:
	- chrome
---

>

有一次开发调试问题时，发现某个A接口请求没有响应，而它下一个B接口请求又依赖这个A接口的返回内容
，于是它认为是这个A接口有问题，把责任都往这个接口上推,因为这是它在chrome控制台看到的现象


{% asset_img size.png  该请求返回数据内容大小 %}

{% asset_img get.png  该接口请求及响应详细内容 %}

{% asset_img timing.png  这响应时间说明并不是因为超时而出现的问题 %}

{% asset_img postman.png  使用postman，该接口是有响应内容的 %}


让人觉得很奇怪，所以我换了个浏览器，使用firefox试试

{% asset_img firefox.png  感觉firefox表现的更差，啥也没有 %}


为此我纪录了浏览器的版本信息

{% asset_img chrome_useragent.png  chrome浏览器User-Agent %}

{% asset_img firefox_useragent.png   firefox浏览器User-Agent %}


但是我在家里