---
title: grafana+prometheus监控服务器
tags: [Linux, 运维]
date:  2019/07/05 17:02
categories: Linux教程
---
#### 背景
------------------
监控服务器状态是运维必不可少的一部分,这里给大家介绍的是一种监控系统grafana,grafana仅仅只是提供界面显示, 所以他需要从influxdb或prometheus中获取数据, 而influxdb或prometheus中的数据又需要从其他地方收集过来, 常用的收集工具是collectd和telegraf, 其中collectd这里不做介绍, 有些数据不是太适合, 而 influxdb 自身集成 telegraf插件, 不需要进行专门的配置

#### 流程
> collectd/telegraf或prometheus插件(收集数据)  ------->   influxdb/prometheus(保存数据)  -------> grafana(显示数据)

###### 下载[node_exporter插件](http://blog.lutao1726.top/node_exporter)

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-arm64.tar.gz
```

###### 解压
```bash
tar xf node_exporter-*.tar.gz
```
###### 在对应机器上启动node_exporter
```bash
setsid ./node_exporter
```
######  查看node_exporter是否开启
```bash
netstat -lntp
tcp6       0      0 :::9100                 :::*                    LISTEN      62737/./node_export
```
###### 修改prometheus配置文件
```lombok.config
 - job_name: 'node_exporter'
   static_configs:
     - targets: ['192.168.1.231:9100']
       labels:
         instance: sys-231  
     - targets: ['192.168.1.68:9100']
        labels:
          instance: sys-68
```
###### 重启prometheus
```bash
nohup ./prometheus --config.file=./prometheus.yml &
```
#### grafana配置
配置prometheus数据源     
添加prometheus插件，然后配置
![Mou_icon](http://blog.lutao1726.top/grafana-redis-01.png)
![Mou_icon](http://blog.lutao1726.top/grafana-redis-02.png)
![Mou_icon](http://blog.lutao1726.top/grafana-redis-03.png)

#### 下载node_exporter仪表盘模板
![Mou_icon](http://blog.lutao1726.top/grafana-node-04.png)
###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-node-06.png)