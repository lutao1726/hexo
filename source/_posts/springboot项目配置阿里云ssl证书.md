---
title: springboot项目配置阿里云ssl证书
date: 2019-01-01 09:00:32
categories: SpringBoot教程
tags: [Spring, Java, SpringBoot]

---
![Mou_icon](http://codeimages.lutao1726.top/springboot.jpg)
<!-- more -->

环境：
-----------------------------------
springboot项目 版本：1.5.8.realse    
证书类型: .pfx  
服务器：阿里云centos主机 
- 申请阿里云ssl证书，免费和贵的都有（或者自己用工具生成的证书用法一致）
- 开放服务器443端口
- 下载证书，将证书放到resource目录下
- application.properties配置
```properties
#证书配置
server.ssl.key-store=classpath:xxxxx.pfx //配置证书路径
server.ssl.key-store-password=xxxx //证书密码
server.ssl.keyStoreType=PKCS12 //keyStoreType就填这个
server.port=443
```
- 项目的入口类Application.java注入两个bean
```java
    @Bean
	public EmbeddedServletContainerFactory servletContainer() {
	//springboot版本不同可能下面的类名会不同或者类的包路径会不同
		TomcatEmbeddedServletContainerFactory tomcat = new TomcatEmbeddedServletContainerFactory() {
			@Override
			protected void postProcessContext(Context context) {
				SecurityConstraint constraint = new SecurityConstraint();
				constraint.setUserConstraint("CONFIDENTIAL");
				SecurityCollection collection = new SecurityCollection();
				collection.addPattern("/*");
				constraint.addCollection(collection);
				context.addConstraint(constraint);
			}
		};
		tomcat.addAdditionalTomcatConnectors(httpConnector());
		return tomcat;
	}

	@Bean
	public Connector httpConnector() {
		Connector connector = new Connector("org.apache.coyote.http11.Http11NioProtocol");
		connector.setScheme("http");
		//监听http的端口号
		connector.setPort(80);
		connector.setSecure(false);
		//监听到http的端口号后转向到的https的端口号
        System.out.println("监听到了80端口");
		connector.setRedirectPort(443);//这里的端口写成和配置文件一样的端口就Ok
		return connector;
	}
```





                                          