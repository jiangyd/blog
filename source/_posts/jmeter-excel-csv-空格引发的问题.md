---
title: jmeter excel csv 空格引发的问题
date: 2018-12-12 13:36:44
tags:
    - jmeter
---


jmeter csv 参数化，需要提供csv文件，如果你使用excel创建csv文件的话，那么这里有一个坑

excel内容，声明下，内容绝对没有空格输入

{% asset_img excel.png %}

jmeter csv配置

{% asset_img jmeter_csv.png %}

http请求配置

{% asset_img jmeter_http.png %}

### http请求详情，内容多了空格，这就是坑

{% asset_img jmeter_detial.png %}



> 为避免出现以上的情况，使用文本编辑器创建csv文件即可，参数之间使用逗号分隔,文件后缀名可以随意，不一定csv

{% asset_img txt.png %}

{% asset_img jmeter_text.png %}

### 参数内容正确
{% asset_img jmeter_text_http.png %}


