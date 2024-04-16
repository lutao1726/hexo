---
title: grafana+prometheus监控nacos
tags: [Linux, 运维]
date:  2019/07/15 17:02
categories: Linux教程
---

###### 安装[Nacos](http://lutao.tk/SpringCloud%20nacos%E8%87%AA%E5%AE%9A%E4%B9%89%E7%94%A8%E6%88%B7/) 

###### 修改配置文件 ***application.properties*** 暴露metrics数据
```bash
management.endpoints.web.exposure.include=*
```
访问{ip}:8848/nacos/actuator/prometheus，看是否能访问到metrics数据

###### 修改prometheus配置文件
```lombok.config
  - job_name: nacos
    metrics_path: '/nacos/actuator/prometheus'
    static_configs:
      - targets: ['192.168.1.238:8848']
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

#### 下载[仪表盘模板](https://github.com/nacos-group/nacos-template/blob/master/nacos-sync-grafana)

###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-nacos-02.png)