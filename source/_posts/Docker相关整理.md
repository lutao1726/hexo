---
title: Docker相关整理
tags: [Linux, 运维, Java]
categories: Linux教程
date:  2018/6/25 16:41
---
<!-- more -->
CentOS Docker 安装
===============

使用 yum 安装
-----------
Docker 要求 CentOS 系统的内核版本高于 3.10 ，查看本页面的前提条件来验证你的CentOS 版本是否支持 Docker 。
```jshelllanguage
    uname -r
```
![](http://www.runoob.com/wp-content/uploads/2016/05/docker08.png)

安装 Docker
-----------
从 2017 年 3 月开始 docker 在原来的基础上分为两个分支版本: Docker CE 和 Docker EE。
Docker CE 即社区免费版，Docker EE 即企业版，强调安全，但需付费使用。
本文介绍 Docker CE 的安装使用。
移除旧的版本：
```jshelllanguage
    sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```
安装一些必要的系统工具：
```jshelllanguage
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
添加软件源信息：
```jshelllanguage
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```
更新 yum 缓存：
```jshelllanguage
sudo yum makecache fast
```
安装docker-ce
```jshelllanguage
sudo yum -y install docker-ce
```
启动 Docker 后台服务
```jshelllanguage
sudo systemctl start docker
```
解决非root用户使用docker的办法
============
创建docker组
```jshelllanguage
sudo groupadd docker
```
将当前用户加入docker组
```jshelllanguage
sudo gpasswd -a ${USER} docker
```
重新启动docker服务（下面是CentOS7的命令）
```jshelllanguage
sudo service docker restart或sudo systemctl restart docker
```
当前用户退出系统重新登陆即可



dockerexec不同用户登入
docker exec -it --user root <容器ID> /bin/bash
![](https://www.2cto.com/uploadfile/Collfiles/20180403/201804030931161219.png)