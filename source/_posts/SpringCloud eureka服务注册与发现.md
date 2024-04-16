---
title: SpringCloud eureka服务注册与发现
date:  2019/5/16 17:02
categories: 框架
tags: [Spring ,SpringCloud , Java]

---
<!-- 展示图片 -->
![Mou_icon](http://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3833687770,1236182247&fm=26&gp=0.jpg)
## 简介

## Eureka Server服务
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.zhjs.base</groupId>
        <artifactId>zhjs-base-parent</artifactId>
        <version>1.1.0-SNAPSHOT</version>
    </parent>
    <groupId>com.zhjs.cloud</groupId>
    <artifactId>zhjs-eureka-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>zhjs-eureka-server</name>
    <description>Demo project for Spring Boot</description>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

    </dependencies>

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
spring.application.name=eureka-server
server.port=1001
eureka.instance.hostname=localhost
eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false
```
* 添加服务启动代码
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;


@EnableEurekaServer
@SpringBootApplication
public class ZhjsEurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZhjsEurekaServerApplication.class, args);
    }

}


```
* 启动
![Mou_icon](http://blog.lutao1726.top/eureka-001.png)
* 在浏览器中打开eureka提供的管理页面

![Mou_icon](http://blog.lutao1726.top/eureka-002.jpg)

## Eureka 服务提供者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.zhjs.base</groupId>
        <artifactId>zhjs-base-parent</artifactId>
        <version>1.1.0-SNAPSHOT</version>
    </parent>
    <groupId>com.zhjs.cloud</groupId>
    <artifactId>zhjs-eureka-client</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>zhjs-eureka-client</name>
    <description>Demo project for Spring Boot</description>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-feign</artifactId>
            <version>1.4.6.RELEASE</version>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>com.zhjs.framework</groupId>
                <artifactId>zhjs-framework-dependencies</artifactId>
                <version>1.1.0-SNAPSHOT</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
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
spring.application.name=eureka-client
server.port=1002
eureka.client.serviceUrl.defaultZone=http://127.0.0.1:1001/eureka/
```
* 添加服务启动代码，并对外提供服务
```java

@EnableEurekaClient
@SpringBootApplication
public class ZhjsEurekaClientApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZhjsEurekaClientApplication.class, args);
    }

}

@RestController
public class DiscoveryController {

    @Autowired
    private DiscoveryClient discoveryClient;

    @Value("${server.port}")
    private String ip;

    @GetMapping("/client")
    public String client() {
        String services = "Services: " + discoveryClient.getServices()+" ip :"+ip;

        System.out.println(services);
        return services;
    }
}

```
* 启动服务提供者之后会在eureka的管理页面中看到eureka-provider相关的服务信息

![Mou_icon](http://blog.lutao1726.top/eureka-003.jpg)



## Eureka 服务消费者
#### 搭建工程
* 引入maven依赖
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>com.zhjs.base</groupId>
        <artifactId>zhjs-base-parent</artifactId>
        <version>1.1.0-SNAPSHOT</version>
    </parent>
    <groupId>com.zhjs.cloud</groupId>
    <artifactId>zhjs-eureka-feign</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>zhjs-eureka-feign</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
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
            <artifactId>spring-boot-starter</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-feign</artifactId>
            <version>1.4.6.RELEASE</version>
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
spring.application.name=eureka-feign
server.port=1003
eureka.client.serviceUrl.defaultZone=http://127.0.0.1:1001/eureka/
```
* 服务消费者可以根据服务名远程调用服务提供者提供的接口
```java
@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class ZhjsEurekaFeignApplication {

    public static void main(String[] args) {
        SpringApplication.run(ZhjsEurekaFeignApplication.class, args);
    }

}


@FeignClient(name = "eureka-client")
public interface DiscoveryService {
    @RequestMapping(value = "/client")
    public String getClient();
}

@RestController
@RequestMapping("/feignTest")
public class TestController {
    @Autowired
    DiscoveryService discoveryService;
    @RequestMapping("/getUserList")
    public String getUserList() {
        return discoveryService.getClient();
    }

}
```
通过调用`http://127.0.0.1:10010/hi`即可获取到服务提供者提供的数据信息

![Mou_icon](http://blog.lutao1726.top/eureka-004.jpg)

通过调用`http://127.0.0.1:10010/services`可以看到consul中提供的相关的服务的信息，存储的信息很简单类似Eureka注册中心

![Mou_icon](http://blog.lutao1726.top/eureka-005.jpg)
