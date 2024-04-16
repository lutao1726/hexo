---
title: grafana+prometheus监控postgresql
tags: [Linux, 运维]
date:  2019/07/09 17:02
categories: Linux教程
---

###### 下载[postgres_exporter插件](http://blog.lutao1726.top/postgres_exporter)

```bash
wget https://github.com/wrouesnel/postgres_exporter/releases/download/v0.5.1/postgres_exporter_v0.5.1_linux-amd64.tar.gz
```

###### 解压
```bash
tar xf postgre_exporter-*.tar.gz
```
###### 启动postgre_exporter
```bash
export DATA_SOURCE_NAME="postgresql://postgres:lt123456@127.0.0.1:5432/postgres?sslmode=disable"
setsid ./postgre_exporter &
```
######  查看postgre_exporter是否开启
```bash
netstat -lntp
tcp        0      0 :::9187                     :::*                        LISTEN      13614/./postgres_ex 
```
###### 在postgres_ 中queries.yaml
```sql
CREATE USER postgres_exporter PASSWORD 'password';
ALTER USER postgres_exporter SET SEARCH_PATH TO postgres_exporter,pg_catalog;

CREATE SCHEMA postgres_exporter AUTHORIZATION postgres_exporter;

CREATE FUNCTION postgres_exporter.f_select_pg_stat_activity()
RETURNS setof pg_catalog.pg_stat_activity
LANGUAGE sql
SECURITY DEFINER
AS $$
  SELECT * from pg_catalog.pg_stat_activity;
$$;

CREATE FUNCTION postgres_exporter.f_select_pg_stat_replication()
RETURNS setof pg_catalog.pg_stat_replication
LANGUAGE sql
SECURITY DEFINER
AS $$
  SELECT * from pg_catalog.pg_stat_replication;
$$;

CREATE VIEW postgres_exporter.pg_stat_replication
AS
  SELECT * FROM postgres_exporter.f_select_pg_stat_replication();

CREATE VIEW postgres_exporter.pg_stat_activity
AS
  SELECT * FROM postgres_exporter.f_select_pg_stat_activity();

GRANT SELECT ON postgres_exporter.pg_stat_replication TO postgres_exporter;
GRANT SELECT ON postgres_exporter.pg_stat_activity TO postgres_exporter;
```
###### 修改prometheus配置文件
```lombok.config
  - job_name: postgre
    static_configs:
    - targets: ['192.168.1.242:9187']   
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
![Mou_icon](http://blog.lutao1726.top/grafana-postgre-04.png)
###### 导入模板
![Mou_icon](http://blog.lutao1726.top/grafana-redis-05.png)
![Mou_icon](http://blog.lutao1726.top/grafana-postgre-06.png)