---
title: linux下如何批量杀JAVA进程或某个进程方法
date: 2019-05-24 14:19:33
tags:
keywords:
description:
---
linux下如何批量杀JAVA进程或某个进程方法
-------
linux下如何批量杀JAVA进程或某个进程方法 

在工作中经常需要停止JAVA进程，停止时间也比较长，那么有时候因为一些情况，需要把 linux 下JAVA所有进程 kill 掉，又不能用killall 直接杀掉某一进程名称包含的所有运行中进程（我们可能只需要杀掉其中的某一类或运行指定参数命令的进程），这个时候我们需要运用ps, grep, cut 和 kill 一起操作。

ok，下面给出具体的参考：
```shell script
ps -ef|grep LOCAL=NO|grep -v grep|cut -c 9-15|xargs kill -9
```
运行这条命令将会杀掉所有含有关键字"LOCAL=NO"的进程，是不是很方便？

下面将这条命令作一下简单说明：

管道符"|"用来隔开两个命令，管道符左边命令的输出会作为管道符右边命令的输入。

*"ps -ef"* 是linux里查看所有进程的命令。这时检索出的进程将作为下一条命令"grep LOCAL=NO"的输入。

*"grep LOCAL=NO"* 的输出结果是，所有含有关键字"LOCAL=NO"的进程。

*"grep -v grep"* 是在列出的进程中去除含有关键字"grep"的进程。

*"cut -c 9-15"* 是截取输入行的第9个字符到第15个字符，而这正好是进程号PID。

*"xargs kill -9"* 中的 xargs 命令是用来把前面命令的输出结果（PID）作为"kill -9"命令的参数，并执行该命令。"kill -9"会强行杀掉指定进程。

其它类似的情况，只需要修改"grep LOCAL=NO"中的关键字部分就可以了。

另一种方法，使用awk
```shell script
ps x|grep gas|grep -v grep |awk '{print $1}'|xargs kill -9
```

