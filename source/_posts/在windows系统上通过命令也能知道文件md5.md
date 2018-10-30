---
title: 在windows系统上通过命令也能知道文件md5
date: 2018-10-23 16:35:52
tags:
	- md5
---


并不是闲的蛋痛记一下在windows系统上计算文件的md5值，主要是公司部分业务测试需要这样做，通过公司被测系统上传文件到windows机器上，验证这个文件是否保持一致性，有没有损坏，自然就要用到md5值


在Linux系统，Mac系统，计算文件的md5并不陌生

Mac系统

```
$ md5 a.png
MD5 (a.png) = e22e153208a3bae4bb64f2166fe76945

```

Linux系统

```
$ md5sum a.png
e22e153208a3bae4bb64f2166fe76945  a.png
```

那么在windows系统呢，说实话，在以前从来没有查询过，今天是第一次知道

```
C:\Users\Administrator\Desktop>certutil -hashfile a.png md5
MD5 哈希(文件 a.png):
e2 2e 15 32 08 a3 ba e4 bb 64 f2 16 6f e7 69 45
CertUtil: -hashfile 命令成功完成。
```