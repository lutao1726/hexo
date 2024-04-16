---
title: SpringCloud Nacos配置中心
date:  2019/6/13 09:02
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](http://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20181220%2F625a58b9a1d3449491d2ae9e396da5d8.jpeg&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1623575506&t=fe07c6007ebc786e268abb7e4480dc0e)
## nacos作为注册中心
1. 先在官网上下载nacos中间件 下面教程有启动步骤
> https://nacos.io/zh-cn/docs/quick-start.html

先新建一个springboot项目，添加如下依赖

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
  <version>0.2.1.RELEASE</version>
</dependency>
```
在resource目录下加入 bootstrap.properties文件 并添加配置中心相关信息
bootstrap.properties：

```properties
#服务名
spring.application.name=nacos-config-example
# 配置中心url
spring.cloud.nacos.config.server-addr=127.0.0.1:8848
```
相应的application.properties的内容写到配置中心里面去，如图所示：

![Mou_icon](http://blog.lutao1726.top/nacos-config-01.png)

![Mou_icon](http://blog.lutao1726.top/nacos-config-02.png)

在项目启动时就会去配置中心去读取配置信息（本地的配置文件application.properties还能用，但优先级低于配置中心的配置）

如果你不想用nacos提供的控制台，nacos也提供了java开发服务端的sdk和api,我们可以用sdk开发配置中心服务端，用java代码去操作配置中心,sdk的文档可参看官方文档。

## 多环境总结
* 第一种：通过DataID与profile实现。
    * 优点：这种方式与Spring Cloud Config的实现非常像，用过Spring Cloud Config的用户，可以毫无违和感的过渡过来，由于命名规则类似，所以要从Spring Cloud Config中做迁移也非常简单。
    * 缺点：这种方式在项目与环境多的时候，配置内容就会显得非常混乱。配置列表中会看到各种不同应用，不同环境的配置交织在一起，非常不利于管理。
    * 建议：项目不多时使用，或者可以结合 Group对项目根据业务或者组织架构做一些拆分规划。
* 第二种：通过Group实现。
    * 优点：通过 Group按环境讲各个应用的配置隔离开。可以非常方便的利用 DataID和 Group的搜索功能，分别从应用纬度和环境纬度来查看配置。
    * 缺点：由于会占用 Group纬度，所以需要对 Group的使用做好规划，毕竟与业务上的一些配置分组起冲突等问题。
    * 建议：这种方式虽然结构上比上一种更好一些，但是依然可能会有一些混乱，主要是在 Group的管理上要做好规划和控制。
* 第三种：通过Namespace实现。
    * 优点：官方建议的方式，通过 Namespace来区分不同的环境，释放了 Group的自由度，这样可以让 Group的使用专注于做业务层面的分组管理。同时，Nacos控制页面上对于 Namespace也做了分组展示，不需要搜索，就可以隔离开不同的环境配置，非常易用。
    * 缺点：没有啥缺点，可能就是多引入一个概念，需要用户去理解吧。
    * 建议：直接用这种方式长远上来说会比较省心。虽然可能对小团队而言，项目不多，第一第二方式也够了，但是万一后面做大了呢？
    
> 多环境注意：不论用哪一种方式实现。对于指定环境的配置（spring.profiles.active=DEV、spring.cloud.nacos.config.group=DEV_GROUP、spring.cloud.nacos.config.namespace=83eed625-d166-4619-b923-93df2088883a），都不要配置在应用的bootstrap.properties中。而是在发布脚本的启动命令中，用-Dspring.profiles.active=DEV的方式来动态指定，会更加灵活！。    

>Nacos使用注意<br>
 Nacos本身的相关配置必须都放在bootstrap.yml文件中
 如果在Nacos添加了应用的配置文件<br> 1. 应用读取配置后只会覆盖本地相同key的配置<br> 2. 应用读取配置后会缓存起来，就算停掉Nacos也会生效
