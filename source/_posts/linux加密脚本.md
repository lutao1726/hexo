---
title: linux加密脚本
tags: [Linux, 运维, Java]
date:  2019/6/18 14:02
categories: Linux教程
---
<!-- more -->

linux加密脚本
=======================
* 1.确保已经安装 bc 和 shc
* 2.shc只能加密bash shell脚本，无法加密expect脚本
```bash
shc -v -r -f auto_deploy.sh
```
执行后会产生两个文件，分别是:     
`auto_deploy.sh.x`    可执行的二进制文件     
`auto_deploy.sh.x.c`  二进制文件的c源码，这个无用，必须删除以防止脚本流程泄露      

接着将.sh.x改名    
```bash
mv auto_deploy.sh.x auto_deploy
```  

## shc相关资料
#### 简介
shc是一个专业的加密shell脚本的工具.它的作用是把shell脚本转换为一个可执行的二进制文件，这个办法很好的解决了脚本中含有IP、密码等不希望公开的问题.

shc的官网下载地址: 
`http://www.datsi.fi.upm.es/~frosal/sources/ `

#### 安装
```bash
tar xzvf shc-3.8.6.tgz
cd shc-3.8.6
mkdir -p /usr/local/man/man1
```
这步是必须的，不然安装过程中会报错，shc将安装命令到/usr/local/bin/目录下；将帮助文档存放在/usr/local/man/man1/目录下，如果系统中无此目录，安装时会报错，可创建此目录后再执行安装

```bash
make install
```

这是要回答yes或者y，不能直接回车，否则会报错
使用方法:
“-f”选项指定需要加密的程序

```bash
[root@localhost ~]# shc -r -f abc.sh 
[root@localhost ~]# ll
total 64
-rw-r--r--. 1 root root    23 Nov 17 11:14 abc.sh
-rwx--x--x. 1 root root 11520 Nov 17 11:14 abc.sh.x
-rw-r--r--. 1 root root  9174 Nov 17 11:14 abc.sh.x.c
drwxr-xr-x. 2 root root  4096 Nov 17 11:13 shc-3.8.7
-rw-r--r--. 1 root root 20498 Nov 17 11:11 shc-3.8.7.tgz
```

运行后会生成两个文件,script-name.x 和 script-name.x.c. 其中script-name.x是加密后的可执行的二进制文件；用./script-name即可运行，script-name.x.c是生成script-name.x的原文件(c语言).
另shc还提供了一种设定有效执行期限的方法，可以首先使用shc将shell程序转化为二进制，并加上过期时间，如：

```bash
[root@localhost ~]#  shc -e 18/10/2006 -m " It's too late to run this script " -f script.sh
```
题外：

如果你仅仅是看不见内容就行了的话，不妨用
```bash
gzexe a.sh
```

原来的 a.sh 就被存为 a.sh~，新的 a.sh 是乱码，但是可以用 sh 的方式运行