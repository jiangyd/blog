---
title: 记一次selenium 一打开浏览器就自动关闭了的问题
date: 2018-05-23 21:53:24
tags:
 - selenium
 - chromedriver
---



selenium无法打开浏览器运行

1.chromedriver 版本与浏览器不匹配
2.如果是在本地调用， localhost需要指向127.0.0.1这个IP，不然也跑不起来(其实只是要host能指向正确的selenium driver的地址就行)


[https://npm.taobao.org/mirrors/chromedriver](https://npm.taobao.org/mirrors/chromedriver)

[https://npm.taobao.org/mirrors/selenium](https://npm.taobao.org/mirrors/selenium)