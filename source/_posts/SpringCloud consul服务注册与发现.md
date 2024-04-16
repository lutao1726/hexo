---
title: SpringCloud consul服务注册与发现
date:  2019/5/22 17:02
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=2103295947,3549244433&fm=26&gp=0.jpg)
## 简介
Consul 是 HashiCorp 公司推出的开源工具，用于实现分布式系统的服务发现与配置。与其他分布式服务注册与发现的方案，Consul的方案更“一站式”，内置了服务注册与发现框 架、分布一致性协议实现、健康检查、Key/Value存储、多数据中心方案，不再需要依赖其他工具（比如ZooKeeper等）。使用起来也较 为简单。Consul使用Go语言编写，因此具有天然可移植性(支持Linux、windows和Mac OS X)；安装包仅包含一个可执行文件，方便部署，与Docker等轻量级容器可无缝配合 。
* 1、Consul 下载地址：https://www.consul.io/downloads.html 
* 2、执行如下命令启动Consul：consul agent -dev
![Mou_icon](http://blog.lutao1726.top/consul-001.png)
* 3、在浏览器中打开consul提供的管理页面
![Mou_icon](http://blog.lutao1726.top/consul-002.jpg)
fgc
## Consul 服务提供者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.lutao</groupId>
    <artifactId>consul-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>consul-provider</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
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
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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
* application.yml中添加相关配置
```yaml
spring:
  cloud:
    consul:
#consul地址
      host: localhost
#consul 对外提供服务的端口
      port: 8500
      discovery:
#服务提供者对外提供的健康检查接口
        healthCheckPath: /health
        healthCheckInterval: 15s
#服务实例ID
        instance-id: consul-provider
#应用名称
  application:
    name: consul-provider
#对外提供服务的端口
server:
  port: 10020
```
* 添加服务启动代码，并对外提供服务
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@EnableDiscoveryClient
@RestController
public class ConsulProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(ConsulProviderApplication.class, args);
    }

    @RequestMapping("/hi")
    public String home() {
        return "hi ,i'm consul-provider";
    }
    /*
     * 自检
     */
    @RequestMapping("/health")
    public String health() {
        return "health";
    }
}

```
* 启动服务提供者之后会在consul的管理页面中看到consul-provider相关的服务信息

![Mou_icon](http://blog.lutao1726.top/consul-003.jpg)



## Consul 服务消费者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.lutao</groupId>
    <artifactId>consul-consumer</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>consul-consumer</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
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
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-consul-discovery</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
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
* application.yml中添加相关配置
```yaml
spring:
  cloud:
    consul:
#consul地址
      host: localhost
#consul 对外提供服务的端口
      port: 8500
      discovery:
        register: false
#应用名称
  application:
    name: consul-consumer
#对外提供服务的端口
server:
  port: 10010
```
* 服务消费者可以根据服务名远程调用服务提供者提供的接口
```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.client.loadbalancer.LoadBalancerClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication

@EnableDiscoveryClient
@RestController
public class ConsulConsumerApplication {
    @Autowired
    RestTemplate restTemplate;

    @Autowired(required=true)
    private LoadBalancerClient loadBalancer;

    @Autowired(required=true)
    private DiscoveryClient discoveryClient;

    /**
     * 从所有服务中选择一个服务（轮询）
     */
    @RequestMapping("/discover")
    public Object discover() {
        return loadBalancer.choose("consul-provider").getUri().toString();
    }
    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }

    @RequestMapping(value = "/hi", method = RequestMethod.GET)
    public String add() {
        return restTemplate.getForEntity("http://consul-provider/hi", String.class).getBody();
    }

    /**
     * 获取所有服务
     */
    @RequestMapping("/services")
    public Object services() {
        return discoveryClient.getInstances("consul-provider");
    }

    public static void main(String[] args) {
        SpringApplication.run(ConsulConsumerApplication.class, args);
    }

}

```
通过调用 `http://127.0.0.1:10010/hi` 即可获取到服务提供者提供的数据信息

![Mou_icon](http://blog.lutao1726.top/consul-004.jpg)

通过调用`http://127.0.0.1:10010/services`可以看到consul中提供的相关的服务的信息，存储的信息很简单类似Eureka注册中心

![Mou_icon](http://blog.lutao1726.top/consul-005.jpg)
