---
title: SpringCloud Nacos集群部署
date:  2019/6/10 09:02
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](http://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20181220%2F625a58b9a1d3449491d2ae9e396da5d8.jpeg&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1623575506&t=fe07c6007ebc786e268abb7e4480dc0e)
## 集群搭建
根据官方文档的介绍，Nacos的集群架构大致如下图所示

![Mou_icon](http://blog.lutao1726.top/nacos集群-01.png)

#### MySQL数据源配置
在进行集群配置之前，先完成对MySQL数据源的初始化和配置。主要分以下两步：
* 1、初始化MySQL数据库，数据库初始化文件：`nacos-mysql.sql`，该文件可以在Nacos程序包下的conf目录下获得。
* 2、修改`conf/application.properties`文件，增加支持MySQL数据源配置，添加（目前只支持mysql）数据源的url、用户名和密码。配置样例如下：

```properties
spring.datasource.platform=mysql
db.num=1
db.url.0=jdbc:mysql://localhost:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true
db.user=root
db.password=
```
#### 集群配置
在Nacos的conf目录下有一个cluster.conf.example，可以直接把example扩展名去掉来使用，也可以单独创建一个`cluster.conf`文件，然后打开将后续要部署的Nacos实例地址配置在这里。

本文以在本地不同端点启动3个Nacos服务端为例，可以如下配置：

```bash
127.0.0.1:8841
127.0.0.1:8842
127.0.0.1:8843
```
>注意：这里的例子仅用于本地学习测试使用，实际生产环境必须部署在不同的节点上，才能起到高可用的效果。另外，Nacos的集群需要3个或3个以上的节点，并且确保这三个节点之间是可以互相访问的。

#### 本地测试
本文中，在集群配置的时候，我们设定了3个Nacos的实例都在本地，只是以不同的端口区分，所以我们在启动Nacos的时候，需要修改不同的端口号。

下面介绍一种方法来方便地启动Nacos的三个本地实例，我们可以将bin目录下的startup.sh脚本复制三份，分别用来启动三个不同端口的Nacos实例，为了可以方便区分不同实例的启动脚本，我们可以把端口号加入到脚本的命名中，比如：

* startup-8841.sh
* startup-8842.sh
* startup-8843.sh

然后，分别修改这三个脚本中的参数，具体如下图的红色部分（端口号根据上面脚本命名分配）：

![Mou_icon](http://blog.lutao1726.top/nacos集群-02.png)

修改完3个脚本配置之后，分别执行下面的命令就可以在本地启动Nacos集群了：

```bash
sh startup-8841.sh
sh startup-8842.sh
sh startup-8843.sh
```

#### 生产环境

在实际生产环境部署的时候，由于每个实例分布在不同的节点上，我们可以直接使用默认的启动脚本（除非要调整一些JVM参数等才需要修改）。只需要在各个节点的Nacos的bin目录下执行`sh startup.sh`命令即可。


#### Proxy配置

在Nacos的集群启动完毕之后，根据架构图所示，我们还需要提供一个统一的入口给我们用来维护以及给Spring Cloud应用访问。简单地说，就是我们需要为上面启动的的三个Nacos实例做一个可以为它们实现负载均衡的访问点。这个实现的方式非常多，这里就举个用Nginx来实现的简单例子吧。

在Nginx配置文件的http段中，我们可以加入下面的配置内容：

![Mou_icon](http://blog.lutao1726.top/nacos集群-03.png)
