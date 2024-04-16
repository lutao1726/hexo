---
title: 完全卸载删除gitlab
date: 2018-09-27 09:00:32
categories: Linux教程
tags: [Linux, 运维, Java]

---
<!-- more -->

完全卸载删除gitlab
------------------
1、停止gitlab
>gitlab-ctl stop

2、卸载gitlab（注意这里写的是gitlab-ce）
>rpm -e gitlab-ce

3、查看gitlab进程
>ps aux | grep gitlab

![](http://blog.whsir.com/wp-content/uploads/2017/05/gitlab.png)


4、杀掉第一个进程（就是带有好多.............的进程）
>kill -9 18777

杀掉后，在ps aux | grep gitlab确认一遍，还有没有gitlab的进程

5、删除所有包含gitlab文件
>find / -name gitlab | xargs rm -rf
