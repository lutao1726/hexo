---
title: 分布式-zookeeper与dubbo
tags: [Java, 分布式]
categories: Java分布式文档
date:  2017/6/19 17:29
---
<!-- more -->

zookeeper和dubbo的关系
=======================
>Dubbo建议使用Zookeeper作为服务的注册中心。

**1、Zookeeper的作用**
>zookeeper用来注册服务和进行负载均衡，哪一个服务由哪一个机器来提供必需让调用者知道，简单来说就是ip地址和服务名称的对应关系。当然也可以通过硬编码的方式把这种对应关系在调用方业务代码中实现，但是如果提供服务的机器挂掉调用者无法知晓，如果不更改代码会继续请求挂掉的机器提供服务。zookeeper通过心跳机制可以检测挂掉的机器并将挂掉机器的ip和服务对应关系从列表中删除。至于支持高并发，简单来说就是横向扩展，在不更改代码的情况通过添加机器来提高运算能力。通过添加新的机器向zookeeper注册服务，服务的提供者多了能服务的客户就多了。

**2、dubbo**
>是管理中间层的工具，在业务层到数据仓库间有非常多服务的接入和服务提供者需要调度，dubbo提供一个框架解决这个问题。
注意这里的dubbo只是一个框架，至于你架子上放什么是完全取决于你的，就像一个汽车骨架，你需要配你的轮子引擎。这个框架中要完成调度必须要有一个分布式的注册中心，储存所有服务的元数据，你可以用zk，也可以用别的，只是大家都用zk。


**3、zookeeper和dubbo的关系**
>Dubbo的将注册中心进行抽象，是得它可以外接不同的存储媒介给注册中心提供服务，有ZooKeeper，Memcached，Redis等。
引入了ZooKeeper作为存储媒介，也就把ZooKeeper的特性引进来。首先是负载均衡，单注册中心的承载能力是有限的，在流量达到一定程度的时候就需要分流，负载均衡就是为了分流而存在的，一个ZooKeeper群配合相应的Web应用就可以很容易达到负载均衡；资源同步，单单有负载均衡还不够，节点之间的数据和资源需要同步，ZooKeeper集群就天然具备有这样的功能；命名服务，将树状结构用于维护全局的服务地址列表，服务提供者在启动的时候，向ZK上的指定节点/dubbo/${serviceName}/providers目录下写入自己的URL地址，这个操作就完成了服务的发布。其他特性还有Mast选举，分布式锁等。

**4、zookeeper和dubbo的的配置**

*生产者配置*
```xml
<dubbo:application name="dubbo_provider_resourcesw"></dubbo:application>
<dubbo:protocol name="dubbo" port="20885" />
<!-- 使用zookeeper注册中心暴露服务地址 -->
<dubbo:registry address="zookeeper://127.0.0.1:2181" check="false" subscribe="false" register=""></dubbo:registry>
<!-- 要暴露的服务接口 -->
<dubbo:service interface="com.cn.xxx.xxxControl" ref="xxxControl" />
```
*消费者配置*
```xml
<dubbo:application name="dubbo_consumer"></dubbo:application>
<!-- 使用zookeeper注册中心暴露服务地址 -->
<dubbo:registry address="zookeeper://127.0.0.1:2181" check="false"></dubbo:registry>
<!-- 要引用的服务 -->
<dubbo:reference interface="com.cn.xxx.xxxControl" id="xxxControl" timeout="180000"></dubbo:reference>
```

**总结小记**
