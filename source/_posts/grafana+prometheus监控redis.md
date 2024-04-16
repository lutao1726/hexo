---
title: grafana+prometheus监控redis
tags: [Linux, 运维]
date:  2019/06/25 17:02
categories: Linux教程
---
#### 背景
------------------
监控服务器状态是运维必不可少的一部分,这里给大家介绍的是一种监控系统grafana,grafana仅仅只是提供界面显示, 所以他需要从influxdb或prometheus中获取数据, 而influxdb或prometheus中的数据又需要从其他地方收集过来, 常用的收集工具是collectd和telegraf, 其中collectd这里不做介绍, 有些数据不是太适合, 而 influxdb 自身集成 telegraf插件, 不需要进行专门的配置

#### 流程
> collectd/telegraf或prometheus插件(收集数据)  ------->   influxdb/prometheus(保存数据)  -------> grafana(显示数据)

###### 下载redis_exporter插件
 ***代理插件不一定非要安装在redis端***
```bash
wget https://github.com/oliver006/redis_exporter/releases/download/v0.30.0/redis_exporter-v0.30.0.linux-amd64.tar.gz
```

###### 解压
```bash
tar xf redis_exporter-v0.30.0.linux-amd64.tar.gz
```
###### 启动redis_exporter登陆redis
```bash
## 无密码
nohup ./redis_exporter -redis.addr 192.168.1.120:6379 &
## 有密码
nohup ./redis_exporter  -redis.addr 192.168.1.120:6379  -redis.password 123456 
```
######  查看redis_exporte是否开启
```bash
netstat -lntp
tcp6       0      0 :::9121               :::*              LISTEN      32407/redis_exporte
```
###### 修改prometheus配置文件
```lombok.config
 - job_name: 'redis'
    static_configs:
    - targets:
      - "localhost:9121"   #redis_exporte在哪台服务器启动的就填哪台服务器ip
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

#### 下载redis仪表盘模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-04.png)
###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-redis-06.png)