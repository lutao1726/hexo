---
title: SpringCloud gateway初体验
date:  2019/5/14 17:29
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](http://justyy.com/wp-content/uploads/2016/01/markdown-syntax-language.png)

## 简介
> Spring Cloud Gateway是Spring Cloud官方推出的第二代网关框架，基于Spring5.0、Spring Boot2.0、Project Reactor等技术开发的网关，使用非阻塞API，Websockets得到支持，目的是代替原先版本中的Spring Cloud Netfilx Zuul，目前Netfilx已经开源了Zuul2.0，但Spring 没有考虑集成，而是推出了自己开发的Spring Cloud GateWay。`这里需要注意一下gateway使用的netty+webflux实现，不要加入web依赖（不要引用webmvc），否则初始化会报错 ，需要加入webflux依赖`。


#### 网关的基本功能
* 网关核心功能是路由转发，因此不要有耗时操作在网关上处理，让请求快速转发到后端服务上
* 网关还能做统一的熔断、限流、认证、日志监控等

![Mou_icon](https://img-blog.csdnimg.cn/20190115094937733.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3podXl1MTk5MTEwMTY1MjA=,size_16,color_FFFFFF,t_70)

## Spring Cloud Gateway VS Spring Cloud Zuul
gateway与zuul的简单比较：gateway使用的是异步请求，zuul是同步请求，gateway的数据封装在ServerWebExchange里，zuul封装在RequestContext里，同步方便调式，可以把数据封装在ThreadLocal中传递。

Spring Cloud Gateway有三个核心概念：路由、断言、过滤器
过滤器：gateway有两种filter：GlobalFilter、GatewayFilter，全局过滤器默认对所有路由有效。


## 搭建工程

#### 引入maven依赖
```xml
<dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-gateway</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
</dependencies> 
       
```

#### 配置yml

#### 测试