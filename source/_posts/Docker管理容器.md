---
title: shipyard安装
tags: [Linux, 运维, Java]
categories: Linux教程
date:  2018/6/30 16:41
---
<!-- more -->
CentOS Docker Shipyard安装
===============
Shipyard介绍
-------------
Shipyard（github）是建立在docker集群管理工具Citadel之上的可以管理容器、主机等资源的web图形化工具,包括core和extension两个版本，core即shipyard主要是把多个 Docker host上的 containers 统一管理（支持跨越多个host），extension即shipyard-extensions添加了应用路由和负载均衡、集中化日志、部署等;Shipyard是在Docker Swarm实现对容器、镜像、docker集群、仓库、节点进行管理的web系统。

了解Shipyard几个概念
-------------------
>  1）engine
   一个shipyard管理的docker集群可以包含一个或多个engine（引擎），一个engine就是监听tcp端口的docker daemon。
   shipyard管理docker daemon、images、containers完全基于Docker API，不需要做其他的修改。
   另外，shipyard可以对每个engine做资源限制，包括CPU和内存；因为TCP监听相比Unix socket方式会有一定的安全隐患，
   所以shipyard还支持通过SSL证书与docker后台进程安全通信。

>   2）rethinkdb
   RethinkDB是一个shipyard项目的一个docker镜像，用来存放账号（account）、引擎（engine）、服务密钥（service key）、
   扩展元数据（extension metadata）等信息，但不会存储任何有关容器或镜像的内容。

Shipyard生态
------------------
shipyard是由shipyard控制器以及周围生态系统构成，以下按照deploy启动顺序进行介绍（下面几个就是shipyard使用脚本安装后，启动的几个容器名）
 
> 1）RethinkDB
 deploy首先启动的就是RethinkDB容器，shipyard采用RethinkDB作为数据库来保存用户等信息

> 2）Discovery
 为了使用Swarm，我们需要一个外部的密钥值存储群容器，shipyard默认是采用了etcd。

> 3）shipyard_certs
 证书管理容器，实现证书验证功能

>4）Proxy
 默认情况下，Docker引擎只监听Socket，我们可以重新配置引擎使用TLS或者使用一个代理容器，转发请求从TCP到Docker监听的UNIX Socket。

> 5）Swarm Manager
 Swarm管理器

> 6）Swarm Agent
 Swarm代理，运行在每个节点上。

> 7）Controller
 shipyard控制器，Remote API的实现和web的实现。


Shipyard部署过程
----------------
部署脚本链接：
http://ou38qmztk.bkt.clouddn.com/deploy.sh

在linux下直接执行命令安装
```jshelllanguage
curl -s http://codeimages.lutao1726.top/deploy.sh | bash -s
```

Shipyard汉化
----------------
汉化脚本链接：
http://codeimages.lutao1726.top/shipyard_cn.sh

在linux下直接执行脚本
```jshelllanguage
curl -s http://codeimages.lutao1726.top/shipyard_cn.sh | bash -s
```
Shipyard节点添加
------------------

在linux下直接执行脚本(192.168.1.237为manager节点ip，替换成自己Shipyard相应的ip)
```jshelllanguage
curl -s  http://codeimages.lutao1726.top/deploy.sh  | ACTION=node DISCOVERY=etcd://://192.168.1.237:4001 bash -s
```


