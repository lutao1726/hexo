---
title: grafana+influxdb+telegraf监控服务器cpu,内存和硬盘
tags: [Linux, 运维]
categories: Linux教程
date:  2019/06/25 16:41
---
背景
------------------
监控服务器状态是运维必不可少的一部分,这里给大家介绍的是一种监控系统grafana,grafana仅仅只是提供界面显示, 所以他需要从influxdb中获取数据, 而influxdb中的数据又需要从其他地方收集过来, 常用的收集工具是collectd和telegraf, 其中collectd这里不做介绍, 有些数据不是太适合, 而 influxdb 自身集成 telegraf插件, 不需要进行专门的配置

流程
> collectd/telegraf(收集数据)  ------->   influxdb(保存数据)  -------> grafana(显示数据)

安装配置
==============
grafana 3.x+
------------------

```bash
cd /opt
# 下载速度很慢, 建议用迅雷下载, 然后通过ftp上载到服务器
wget https://grafanarel.s3.amazonaws.com/builds/grafana-3.1.1-1470047149.x86_64.rpm
yum localinstall grafana-3.1.1-1470047149.x86_64.rpm

# 启动服务 (centos6)
service grafana-server start
# 添加开机启动 (centos6)
chkconfig --add grafana-server

# 启动服务 (centos7)
systemctl start grafana-server 
# 添加开机启动 (centos7)
systemctl enable grafana-server 

```
默认启动端口 3000, 账户密码默认都是 admin

influxdb 1.x+
-----------------
```bash
cd /opt
# 下载速度很慢, 建议用迅雷下载, 然后通过ftp上载到服务器
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.0.2.x86_64.rpm  
yum localinstall influxdb-1.0.2.x86_64.rpm
# 启动服务 (centos6)
service influxdb start
# 启动服务 (centos7)
systemctl start influxdb

```
管理后台默认端口: 8083, 我们可以直接访问后台管理数据
数据传递默认端口: 8086, 其他服务传递数据的端口

telegraf 1.x+
------------
```bash
cd /opt
# 下载速度很慢, 建议用迅雷下载, 然后通过ftp上载到服务器
wget https://dl.influxdata.com/telegraf/releases/telegraf-1.0.1.x86_64.rpm
yum localinstall telegraf-1.0.1.x86_64.rpm

cd /etc/telegraf
# 设置将数据传递写入influxdb服务器
vi telegraf.conf
# 将地址改成 influxdb 对应的服务器地址, 端口默认 8086, 默认数据库 telegraf
#如果只想收集cpu, 内存和硬盘的数据
telegraf -sample-config -input-filter cpu:mem:disk -output-filter influxdb > telegraf.conf
# 启动服务 (centos6)
service telegraf start
# 启动服务 (centos7)
systemctl start telegraf

```

grafana 使用
==================
* 创建数据源
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_2ck6.png)
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_FPpU.png)


* 创建显示面板
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_AqKx.png)
* 添加单个面板
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_JvQt.png)
* 选择定义数据源
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_orfY.png)
* 从数据源中添加数据
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_ZiJM.png)
* 最终显示结果:
![Mou_icon](https://static.oschina.net/uploads/img/201712/30005624_9idB.png)

模板例子
=========
在[grafana官网](https://grafana.com/dashboards)上有许多模板
![](http://blog.lutao1726.top/grafana-0001.jpg)
![](http://blog.lutao1726.top/grafana-0002.jpg)
监控服务器模板链接
http://blog.lutao1726.top/system_rev4.json

效果图
![](http://blog.lutao1726.top/grafana-0003.jpg)




