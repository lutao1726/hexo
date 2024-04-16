---
title: grafana+prometheus监控Jvm
tags: [Linux, 运维]
date:  2019/08/03 17:02
categories: Linux教程
---
###### 下载jmx_exporter
```bash
mkdir -p /app/runtime/prometheus/jmx_exporter
 cd /app/runtime/prometheus/jmx_exporter
 wget https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/0.3.1/jmx_prometheus_javaagent-0.3.1.jar
```
###### 添加配置文件 ***simple-config.yml***

```yaml

startDelaySeconds: 0
hostPort: 127.0.0.1:1234
username: 
password: 
jmxUrl: service:jmx:rmi:///jndi/rmi://127.0.0.1:1234/jmxrmi
ssl: false
lowercaseOutputName: false
lowercaseOutputLabelNames: false
whitelistObjectNames: ["org.apache.cassandra.metrics:*"]
blacklistObjectNames: ["org.apache.cassandra.metrics:type=ColumnFamily,*"]
rules:
  - pattern: 'org.apache.cassandra.metrics<type=(\w+), name=(\w+)><>Value: (\d+)'
    name: cassandra_$1_$2
    value: $3
    valueFactor: 0.001
    labels: {}
    help: "Cassandra metric $1 $2"
    type: GAUGE
    attrNameSnakeCase: false

```
###### 启动jar包
```bash
#!/bin/bash -ile 
nohup java -jar -Dsun.zip.disableMemoryMapping=true   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/app/services/saas-data-center -Xms512m -Xmx1024m -javaagent:/app/runtime/prometheus/jmx_exporter/jmx_prometheus_javaagent-0.3.1.jar=6060:/app/runtime/prometheus/jmx_exporter/simple-config.yml  saas-data-center-0.0.1-SNAPSHOT.jar > /app/logs/saas-data-center/saas-data-center.log 2>&1 &
echo $! > /app/data/data-saas-data-center.pid
```

###### 修改prometheus配置文件
```lombok.config
  - job_name: 'jvm'
    scrape_interval: 30s
    static_configs:
    - targets:
      - '192.168.1.231:6060'
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
![Mou_icon](http://blog.lutao1726.top/grafana-jvm-01.png)
###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-jvm-02.png)
