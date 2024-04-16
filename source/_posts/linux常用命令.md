---
title: linux常见命令
tags: [Linux, 运维, Java]
categories: Linux教程
date:  2019/03/18 14:02
---
<!-- more -->

linux常见命令
=======================
查看文件夹内存：
````shell
du -ah --max-depth=1
#a表示显示目录下所有的文件和文件夹（不含子目录），h表示以人类能看懂的方式，max-depth表示目录的深度   
````


<!-- npm install chromedriver --chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver -->

sed替换变量
今天在写脚本时用到了sed，我用sed替换xml文件中的变量。一般在sed 中替换都用单引号，如下边

> sed -i ‘s/10/1000/g’ test.xml         

但是如果需要把1000改成变量，如
>sed -i ’s/10/$num/g‘ test.xml

这样就不成功。

此时需要把单引号改成双引号,如下边例子
>$num=1000  
sed -i "s/10/$num/g" test.xml



CentOS或者linux 上的IP丢失问题  
Linux renew ip command(https://blog.csdn.net/llziseweiqiu/article/details/79210396)
>$ sudo dhclient -r //release ip 释放IP   
 $ sudo dhclient //获取IP


给用户添加sudo权限
===============
1、切换到超级用户
----------------
2、修改sudoers文件内容
--------------
a) 给文件sudoers 增加write权限
 >   命令：#chmod u+w /etc/sudoers 
 
b) 给用户添加sudo权限
 >  命令：#vim /etc/sudoers
 xxxx   ALL=(ALL)      ALL
 
 保存退出:wq
c) 修改完毕将sudoers文件的写权限去掉(也可以不去掉)
 > 命令：#chmod u-w /etc/sudoers
 
 
 
 cpu
 > more /proc/cpuinfo | grep "model name"
 
 查看linux版本
 >more /etc/redhat-release
 
 shell语法：
 1. if elif else fi
```bash
if [ $1x == "ab" ]; then
    echo "you had enter ab"
elif [ $1x == "cd" ]; then
    echo "you had enter cd"
else
    echo "you had enter unexpected word"
fi
``` 

应用重启
```bash
#!/bin/bash -ile
PROJECT_NAME=saas-xxx-sit
pid=`ps -ef |grep $PROJECT_NAME |grep -v "grep" |awk '{print $2}'`
if [ $pid ];then
	echo "App  is  running  and pid=$pid, restart it"
	kill -9 $pid
	
fi
nohup java -jar /app/services/saas-xxx-sit/saas-xxx-0.0.1-SNAPSHOT.jar -Dorg.terracotta.quartz.skipUpdateCheck=true > /app/logs/saas-xxx-sit/saas-xxx-sit.log 2>&1 &
ps -aux|grep saas-xxx-sit	
```