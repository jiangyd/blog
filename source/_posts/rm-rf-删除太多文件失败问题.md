---
title: rm -rf 删除太多文件失败问题
date: 2018-08-13 13:08:47
tags:
 - rm
---

### 某目录下有10多万个小文件，rm 无法删除

```
root@3ac80f67141a:/autotest/test/reports/allure-results# ls -l|wc -l
136043
root@3ac80f67141a:/autotest/test/reports/allure-results# rm -rf *
bash: /bin/rm: Argument list too long
```

### 解决方法，文件一个一个删除,一定要注意当前路径

```
root@3ac80f67141a:~# cd /autotest/test/reports/allure-results/
root@3ac80f67141a:/autotest/test/reports/allure-results# ls -l|awk '{print "rm -rf ",$9}'|sh
```
