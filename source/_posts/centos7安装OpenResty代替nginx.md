---
title: centos7安装OpenResty代替nginx
date:  2019/6/19 16:41
categories: 框架
tags: [Linux , 运维, Java]

---
<!-- 展示图片 -->
![Mou_icon](http://justyy.com/wp-content/uploads/2016/01/markdown-syntax-language.png)
<!-- more -->

# centos7安装OpenResty代替nginx
## 1.安装依赖的软件包

```bash
yum install readline-devel pcre-devel openssl-devel gcc
```
##  2.安装openresty

建立目录： 

    源代码编译目录： /app/runtimes/openresty

    安装执行目录： /app/services/openresty

下载openresty源码：  

进入：`cd /app/runtimes/openresty`

下载：`wget https://openresty.org/download/openresty-1.11.2.5.tar.gz`

解压：`tar -zxvf openresty-1.11.2.5.tar.gz`

## 3.安装LuaJIT
进入：
  > cd openresty-1.11.2.5/bundle/LuaJIT-2.1-20170808/

清理安装：
  > make clean && make && make install
  
## 4.下载ngx_cache_purge模块，该模块用于清理nginx缓存  

```bash
cd ../

wget https://github.com/FRiCKLE/ngx_cache_purge/archive/2.3.tar.gz

tar -xvf 2.3.tar.gz
```

## 5.下载nginx_upstream_check_module模块，该模块用于ustream健康检查

```bash
wget https://github.com/yaoweibin/nginx_upstream_check_module/archive/v0.3.0.tar.gz

tar -xvf v0.3.0.tar.gz
```

## 6.安装openresty

   > cd /app/runtimes/openresty/openresty-1.11.2.5/

编译：
   > ./configure --prefix=/app/services/openresty --with-http_realip_module --with-pcre --with-luajit --add-module=./bundle/ngx_cache_purge-2.3/ --add-module=./bundle/nginx_upstream_check_module-0.3.0/ -j2 


安装：
   > make && make install
   
## 7.添加nginx到服务加入开机启动   
```bash
vi /lib/systemd/system/nginx.service

    [Unit]

    Description=nginx

    After=network.target



    [Service]

    Type=forking

    ExecStart=/app/services/openresty/nginx/sbin/nginx

    ExecReload=/app/services/openresty/nginx/sbin/nginx -s reload

    ExecStop=/app/services/openresty/nginx/sbin/nginx -s quit

    PrivateTmp=true

    [Install]

    WantedBy=multi-user.target
```

## 8.启动并加入开机启动：
> systemctl start nginx.service     
  systemctl enable nginx.service
  
## 9.将nginx加入到环境变量    
```bash
vi /etc/profile,加入

# nginx
export NGINX_HOME=/app/services/openresty/nginx
export PATH=$PATH:$NGINX_HOME/sbin

刷新:source /etc/profile
```
