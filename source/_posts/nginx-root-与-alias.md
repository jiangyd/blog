---
title: nginx root 与 alias
date: 2018-08-09 14:29:18
tags: 
  - root
  - alias
categories: "nginx"
---


### 先看看nginx的配置文件

```
server {
  listen 8090;
  server_name 192.168.56.102;

  location /root {
    index index.html;
    root /home;
  }

  location /alias {
    index index.html;
    alias /home;
  }

}
```

#### 先访问 http://192.168.56.102/root

{% asset_img root.png  访问的实际文件是/home+/root+index.html %}


#### 再试试访问 http://192.168.56.102/alias

{% asset_img alias.png  访问的实际文件是 /home+index.html %}



> nginx 中在location中使用root，路径为 root定义的目录(/home)＋访问的目录(/root)+具体文件

> 在location中使用alias，路径为 root定义的目录(/home)+具体路径文件



#### 假设我想通过/alias访问 /home/root/index.html这个文件我应该怎么做?

{% asset_img alias_root.png  %}

