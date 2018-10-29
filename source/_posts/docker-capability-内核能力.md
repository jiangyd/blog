---
title: docker capability(能力)
date: 2018-10-19 09:34:20
tags:
	- docker
---

我同事遇到一个问题，需要修改系统时间，方便测试，由于应用是通过容器部署的，所以第一个想到的是修改容器时间,但是问题来了，修改容器时间报错，不允许修改

```
root@ed5a0990010a:/# date -s '2018-10-19 09:38:00'
date: cannot set date: Operation not permitted
Fri Oct 19 09:38:00 UTC 2018

```

不知道是什么原因，作为临时的解决方案，在容器宿主机上修改了时间，然后重启容器，达到可以测试的条件

但是这么蛮干肯定是不行的，该宿主机可能部署其它应用，导致其它应用出现各种问题，所以事后马上进行了各种搜索，在容器里面为什么修改时间报错

搜索的结果发现与capability（Linux内核能力）有关系，意思是说修改时间需要有SYS_TIME权限，而容器默认启动进去虽然是root用户，但是并没有这个capability

查看了官方的文档，文档中列出了一些容器默认的capability

https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities


Capability Key | Capability Description |
---|---
SETPCAP	| Modify process capabilities.
MKNOD	| Create special files using mknod(2).
AUDIT_WRITE	| Write records to kernel auditing log.
CHOWN	| Make arbitrary changes to file UIDs and GIDs (see chown(2)).
NET_RAW	| Use RAW and PACKET sockets.
DAC_OVERRIDE |	Bypass file read, write, and execute permission checks.
FOWNER	| Bypass permission checks on operations that normally require the file system UID of the process to match the UID of the file.
FSETID	| Don’t clear set-user-ID and set-group-ID permission bits when a file is modified.
KILL	| Bypass permission checks for sending signals.
SETGID	| Make arbitrary manipulations of process GIDs and supplementary GID list.
SETUID	| Make arbitrary manipulations of process UIDs.
NET_BIND_SERVICE |	Bind a socket to internet domain privileged ports (port numbers less than 1024).
SYS_CHROOT	| Use chroot(2), change root directory.
SETFCAP	| Set file capabilities.


其它一些并不是默认的capability

Capability Key	| Capability Description |
---|---
SYS_MODULE	| Load and unload kernel modules.
SYS_RAWIO	| Perform I/O port operations (iopl(2) and ioperm(2)).
SYS_PACCT	| Use acct(2), switch process accounting on or off.
SYS_ADMIN	| Perform a range of system administration operations.
SYS_NICE	| Raise process nice value (nice(2), setpriority(2)) and change the nice value for arbitrary processes.
SYS_RESOURCE	| Override resource Limits.
SYS_TIME	| Set system clock (settimeofday(2), stime(2), adjtimex(2)); set real-time (hardware) clock.
SYS_TTY_CONFIG	| Use vhangup(2); employ various privileged ioctl(2) operations on virtual terminals.
AUDIT_CONTROL	| Enable and disable kernel auditing; change auditing filter rules; retrieve auditing status and filtering rules.
MAC_ADMIN	| Allow MAC configuration or state changes. Implemented for the Smack LSM.
MAC_OVERRIDE	| Override Mandatory Access Control (MAC). Implemented for the Smack Linux Security Module (LSM).
NET_ADMIN	| Perform various network-related operations.
SYSLOG	| Perform privileged syslog(2) operations.
DAC_READ_SEARCH	| Bypass file read permission checks and directory read and execute permission checks.
LINUX_IMMUTABLE	| Set the FS_APPEND_FL and FS_IMMUTABLE_FL i-node flags.
NET_BROADCAST	| Make socket broadcasts, and listen to multicasts.
IPC_LOCK	| Lock memory (mlock(2), mlockall(2), mmap(2), shmctl(2)).
IPC_OWNER	| Bypass permission checks for operations on System V IPC objects.
SYS_PTRACE	| Trace arbitrary processes using ptrace(2).
SYS_BOOT	| Use reboot(2) and kexec_load(2), reboot and load a new kernel for later execution.
LEASE	| Establish leases on arbitrary files (see fcntl(2)).
WAKE_ALARM	| Trigger something that will wake up the system.
BLOCK_SUSPEND	| Employ features that can block system suspend.


看到这，我们就是要解决问题了

第一种办法：给予容器完全控制权，就等同于真正的root用户
```
$ docker run -it --privileged ubuntu:16.04 /bin/bash
root@1d77d912f940:/# date -s "2018-10-19 13:25:00"
Fri Oct 19 13:25:00 UTC 2018
root@1d77d912f940:/# date
Fri Oct 19 13:25:01 UTC 2018
root@1d77d912f940:/#
```

第二种办法: 给容器增加capability

```
$ docker run -it --cap-add SYS_TIME ubuntu:16.04 /bin/bash
root@2d99ab6424c1:/# date -s "2018-10-19 14:20:21"
Fri Oct 19 14:20:21 UTC 2018
root@2d99ab6424c1:/# date
Fri Oct 19 14:20:22 UTC 2018
root@2d99ab6424c1:/#
```

--cap-add 与 --privileged 相比，颗粒度更细，可以想要什么capability 就增加什么capability，与之对应的还有 --cap-drop (去掉某些capability)


问题貌似确实解决了，但是还是有问题的，我容器已经在运行中了，难道要重新给我搞一个容器，不能更新已有的容器吗

