---
title: grafana+prometheus监控jenkins
tags: [Linux, 运维]
date:  2019/07/28 17:02
categories: Linux教程
---

###### 在jenkins中下载prometheus-plugin
![Mou_icon](http://blog.lutao1726.top/grafana-jenkins-01.png)

###### 修改prometheus配置文件
```lombok.config
  - job_name: 'jenkins'
    metrics_path: '/prometheus/'
    scheme: http
    bearer_token: <bearer_token>
    static_configs:
    - targets: ['192.168.1.68:8090']
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
![Mou_icon](http://blog.lutao1726.top/grafana-jenkins-02.png)
###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-jenkins-03.png)
