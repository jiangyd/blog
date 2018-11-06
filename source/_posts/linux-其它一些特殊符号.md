---
title: linux 其它一些特殊符号
date: 2018-11-06 17:49:43
tags:
	- linux
---


### ''

单引号(英文状态)，在单引号中所有特殊字符都没有特殊含义，例如 $,\`\`


```
# name='jyd'
# echo $name
jyd
# echo '$name'
$name
# echo '`ls`'
`ls`



#如果是双引号就可以执行命令
# echo "`ls`"   
a1.txt
a2.txt
a3.txt
abc.txt
ab.txt
ad.txt
a.txt
```


### “”

双引号(英文状态)，在双引号中所有特殊字符都没有特殊含义，但是 $,\`\`,\\例外

```
# echo "$name"
jyd

# echo "`pwd`"
/root/day

# echo "\$name"
$name
```


### \`\`

反引号，反引号里面包裹的是系统命令

```
# echo `ls`
a1.txt a2.txt a3.txt abc.txt ab.txt ad.txt a.txt

# echo `pwd`
/root/day
```


### $()

$()，包裹的也是系统命令，这个与反引号作用一样，但是这个比较常用

```
# echo $(ls)
a1.txt a2.txt a3.txt abc.txt ab.txt ad.txt a.txt

# echo $(pwd)
/root/day
```



###   \# 号


\#号在脚本里面是注释

### $

$ ，是调用变量的

```
# name='jyd'
# echo $name
jyd
```
