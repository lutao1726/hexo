---
title: grafana+prometheus监控mysql
tags: [Linux, 运维]
date:  2019/07/21 17:02
categories: Linux教程
---

#### 下载[mysqld_exporter插件](https://github.com/prometheus/mysqld_exporter)

###### 在被监控端mysql服务器上创建账号用于mysql exporter收集使用
```sql
GRANT REPLICATION CLIENT, PROCESS ON  *.*  to 'exporter'@'%' identified by '123456';
GRANT SELECT ON performance_schema.* TO 'exporter'@'%';
flush privileges;
```



```bash
wget https://github.com/prometheus/mysqld_exporter/releases/download/v0.12.1/mysqld_exporter-0.12.1.linux-amd64.tar.gz
```

###### 解压
```bash
tar xf tar -xvf mysqld_exporter-*.tar.gz -C /app/runtime/prometheus
```
###### 配置文件 ***.my.cnf***
```cnf
[client]
user=exporter
password=123456
```

###### 在启动
```bash
./mysqld_exporter -config.my-cnf=".my.cnf" &
```

######  查看node_exporter是否开启
```bash
netstat -lntp
tcp6       0      0 :::9104                 :::*                    LISTEN      4981/./mysqld_expor
```
###### 修改prometheus配置文件
```lombok.config
  - job_name: mysql
    static_configs:
      - targets: ['118.25.46.244:9104']
        labels:
          instance: db1 
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

#### 下载仪表盘模板
![Mou_icon](http://blog.lutao1726.top/grafana-mysql-01.png)

###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-mysql-02.png)