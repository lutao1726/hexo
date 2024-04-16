---
title: SpringCloud Nacos服务注册与发现
date:  2019/6/01 17:02
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](http://gimg2.baidu.com/image_search/src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20181220%2F625a58b9a1d3449491d2ae9e396da5d8.jpeg&refer=http%3A%2F%2F5b0988e595225.cdn.sohucs.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1623575506&t=fe07c6007ebc786e268abb7e4480dc0e)
## 简介
Nacos致力于帮助您发现、配置和管理微服务。Nacos提供了一组简单易用的特性集，帮助您快速实现动态服务发现、服务配置、服务元数据及流量管理。Nacos帮助您更敏捷和容易地构建、交付和管理微服务平台。Nacos是构建以“服务”为中心的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。
在接下里的教程中，将使用Nacos作为微服务架构中的注册中心
* 1、Nacos 下载地址：https://github.com/alibaba/nacos/releases
* 2、执行如下命令启动Nacos：cmd startup.cmd -m standalone
![Mou_icon](http://blog.lutao1726.top/nacos-001.png)
* 3、在浏览器中打开Nacos提供的管理页面(用户名，密码为  nacos ``可配置`` )
![Mou_icon](http://blog.lutao1726.top/nacos-002.jpg)

## Nacos 服务提供者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.lutao</groupId>
    <artifactId>nacos-client</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>nacos-client</name>
    <description>Demo project for nacos</description>

    <properties>
        <java.version>1.8</java.version>
        <nacos.version>0.2.1.RELEASE</nacos.version>
        <spring-boot.version>2.0.4.RELEASE</spring-boot.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
            <version>${nacos.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <version>${nacos.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```
* application.properties中添加相关配置
```properties
server.port=8070
spring.application.name=example
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```
* 添加服务启动代码，并对外提供服务
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
public class NacosClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosClientApplication.class, args);
    }
    @RestController
    class EchoController {
        @RequestMapping(value = "/echo/{string}", method = RequestMethod.GET)
        public String echo(@PathVariable String string) {
            return "Hello Nacos Discovery " + string;
        }
    }
}

```

* 启动服务提供者之后会在nacos的管理页面中看到nacos-provider相关的服务信息

![Mou_icon](http://blog.lutao1726.top/nacos-003.jpg)



## Nacos 服务消费者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.lutao</groupId>
    <artifactId>nacos-consumer</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>nacos-consumer</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <nacos.version>0.2.1.RELEASE</nacos.version>
        <spring-boot.version>2.0.4.RELEASE</spring-boot.version>
        <spring-cloud.version>Finchley.RELEASE</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
            <version>${nacos.version}</version>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>


```
* application.properties中添加相关配置
```properties
server.port=8080
spring.application.name=service-consumer
spring.cloud.nacos.discovery.server-addr=127.0.0.1:8848
```
* 服务消费者可以根据服务名远程调用服务提供者提供的接口
```java
package com.lutao.nacosconsumer;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.context.annotation.Bean;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableDiscoveryClient
public class NacosConsumerApplication {
    @LoadBalanced
    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
    public static void main(String[] args) {
        SpringApplication.run(NacosConsumerApplication.class, args);
    }
    @RestController
    public class TestController {

        private final RestTemplate restTemplate;

        @Autowired(required=true)
        private DiscoveryClient discoveryClient;

        @Autowired
        public TestController(RestTemplate restTemplate) {this.restTemplate = restTemplate;}

        @RequestMapping(value = "/echo/{str}", method = RequestMethod.GET)
        public String echo(@PathVariable String str) {
            return restTemplate.getForObject("http://example/echo/" + str, String.class);
        }
        /**
         * 获取所有服务
         */
        @RequestMapping("/services")
        public Object services() {
            return discoveryClient.getInstances("example");
        }
    }
}

```
通过调用`http://127.0.0.1:8080/echo/nacosclient`即可获取到服务提供者提供的数据信息

![Mou_icon](http://blog.lutao1726.top/nacos-004.jpg)

通过调用`http://127.0.0.1:8080/services`可以看到Nacos中提供的相关的服务的信息，存储的信息很简单类似Eureka注册中心

![Mou_icon](http://blog.lutao1726.top/nacos-005.jpg)

## Nacos 注意事项
#### 兼容版本
| SpringCloud  | SpringBoot  | Spring Cloud Alibaba|
| :--------:   | :-----:   | :----: |
|     Greenwich       |   2.1.x      |   0.2.2(还没有RELEASE)     |
|     Finchley     |      2.0.x    |     0.2.1   |
|     Edgware       |     1.5.x    |     0.1.1   |
|     Dalston      |      1.5.x    |     0.1.1   |

在SpringBoot 2.0.x以下时 Spring Cloud Alibaba为0.1.1 启动报错

```bash
2019/06/11 15:10:30 INFO  [main] [org.springframework.boot.autoconfigure.logging.AutoConfigurationReportLoggingInitializer] : 

Error starting ApplicationContext. To display the auto-configuration report re-run your application with 'debug' enabled.
2019/06/11 15:10:30 ERROR [main] [org.springframework.boot.SpringApplication] : Application startup failed
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'nacosAutoServiceRegistration' defined in class path resource [org/springframework/cloud/alibaba/nacos/NacosDiscoveryAutoConfiguration.class]: Bean instantiation via factory method failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.cloud.alibaba.nacos.registry.NacosAutoServiceRegistration]: Factory method 'nacosAutoServiceRegistration' threw exception; nested exception is java.lang.NoSuchMethodError: org.springframework.cloud.client.serviceregistry.AbstractAutoServiceRegistration.<init>(Lorg/springframework/cloud/client/serviceregistry/ServiceRegistry;Lorg/springframework/cloud/client/serviceregistry/AutoServiceRegistrationProperties;)V
	at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:599)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.instantiateUsingFactoryMethod(AbstractAutowireCapableBeanFactory.java:1173)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBeanInstance(AbstractAutowireCapableBeanFactory.java:1067)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:513)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:483)
	at org.springframework.beans.factory.support.AbstractBeanFactory$1.getObject(AbstractBeanFactory.java:306)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:230)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:302)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:197)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:761)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:867)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:543)
	at org.springframework.boot.context.embedded.EmbeddedWebApplicationContext.refresh(EmbeddedWebApplicationContext.java:122)
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:693)
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:360)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:303)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1118)
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1107)
	at cn.zhangfusheng.alibaba.nacos.test.server.AlibabaNacosTestServerApplication.main(AlibabaNacosTestServerApplication.java:21)
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.cloud.alibaba.nacos.registry.NacosAutoServiceRegistration]: Factory method 'nacosAutoServiceRegistration' threw exception; nested exception is java.lang.NoSuchMethodError: org.springframework.cloud.client.serviceregistry.AbstractAutoServiceRegistration.<init>(Lorg/springframework/cloud/client/serviceregistry/ServiceRegistry;Lorg/springframework/cloud/client/serviceregistry/AutoServiceRegistrationProperties;)V
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:189)
	at org.springframework.beans.factory.support.ConstructorResolver.instantiateUsingFactoryMethod(ConstructorResolver.java:588)
	... 18 common frames omitted
Caused by: java.lang.NoSuchMethodError: org.springframework.cloud.client.serviceregistry.AbstractAutoServiceRegistration.<init>(Lorg/springframework/cloud/client/serviceregistry/ServiceRegistry;Lorg/springframework/cloud/client/serviceregistry/AutoServiceRegistrationProperties;)V
	at org.springframework.cloud.alibaba.nacos.registry.NacosAutoServiceRegistration.<init>(NacosAutoServiceRegistration.java:43)
	at org.springframework.cloud.alibaba.nacos.NacosDiscoveryAutoConfiguration.nacosAutoServiceRegistration(NacosDiscoveryAutoConfiguration.java:62)
	at org.springframework.cloud.alibaba.nacos.NacosDiscoveryAutoConfiguration$$EnhancerBySpringCGLIB$$5cdf832e.CGLIB$nacosAutoServiceRegistration$2(<generated>)
	at org.springframework.cloud.alibaba.nacos.NacosDiscoveryAutoConfiguration$$EnhancerBySpringCGLIB$$5cdf832e$$FastClassBySpringCGLIB$$531121d8.invoke(<generated>)
	at org.springframework.cglib.proxy.MethodProxy.invokeSuper(MethodProxy.java:228)
	at org.springframework.context.annotation.ConfigurationClassEnhancer$BeanMethodInterceptor.intercept(ConfigurationClassEnhancer.java:358)
	at org.springframework.cloud.alibaba.nacos.NacosDiscoveryAutoConfiguration$$EnhancerBySpringCGLIB$$5cdf832e.nacosAutoServiceRegistration(<generated>)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.beans.factory.support.SimpleInstantiationStrategy.instantiate(SimpleInstantiationStrategy.java:162)
	... 19 common frames omitted
Disconnected from the target VM, address: '127.0.0.1:52284', transport: 'socket'

Process finished with exit code 1
```
查看源码_如图报错

![Mou_icon](http://blog.lutao1726.top/nacos-0006.png)
* NacosAutoServiceRegistration 继承 AbstractAutoServiceRegistration,AbstractAutoServiceRegistration的构造函数值接收一个ServiceRegistry serviceRegistry

解决问题
* 查看 AbstractAutoServiceRegistration 所在的依赖关系

![Mou_icon](http://blog.lutao1726.top/nacos-0007.png)

* 限定 spring-cloud-Commons的版本号.当前的1.3.0 版本中AbstractAutoServiceRegistration的构造函数接收单个参数

#### 通过dependencyManagement限制版本

```xml
<dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Edgware.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>0.1.1.RELEASE</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
           <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-commons</artifactId>
                <version>1.3.5.RELEASE</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
```
