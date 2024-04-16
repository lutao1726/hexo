---
title: Spring AOP 实现数据库读写分离
date: 2017-08-12 09:00:32
categories: Spring教程
tags: [Spring, Java, Mysql]

---
![Mou_icon](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=3833687770,1236182247&fm=26&gp=0.jpg)
<!-- more -->

 背景
============================
我们一般应用对数据库而言都是“读多写少”，也就说对数据库读取数据的压力比较大，有一个思路就是说采用数据库集群的方案，
其中一个是主库，负责写入数据，我们称之为：写库；
其它都是从库，负责读取数据，我们称之为：读库；
那么，对我们的要求是：
>- 读库和写库的数据一致；
>- 写数据必须写到写库；
>- 读数据必须到读库；

方案
============================
目前公司已经对数据库进行了双机热备，保证了读库和写库的数据一致性，
 所以目前要做的就是解决读写分离
>解决读写分离的方案有两种：应用层解决和中间件解决。

应用层解决
---------------------------
![Mou_icon](http://ou38qmztk.bkt.clouddn.com/read_write_model.jpg)

优点：
> 1.多数据源切换方便，由程序自动完成；
> 2.不需要引入中间件；
> 3.理论上支持任何数据库；

缺点：
> 1.由程序员完成，运维参与不到；
> 2.不能做到动态增加数据源；

中间件解决
-----------------------
![Mou_icon](http://ou38qmztk.bkt.clouddn.com/read_write_model2.jpg)

优点：
>1.源程序不需要做任何改动就可以实现读写分离；
>2.动态添加数据源不需要重启程序；

缺点：
>1.程序依赖于中间件，会导致切换数据库变得困难；
>2.由中间件做了中转代理，性能有所下降；

本文我们介绍一种在应用层的解决方案，通过spring动态数据源和AOP来解决数据库的读写分离。

使用Spring基于应用层实现
======================

数据源配置
----------------------
1.读（jdbc.properties）
``` properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.8.19:3306/newagency?useUnicode=true&characterEncoding=UTF-8
username=***
password=***
#password=123
#定义初始连接数
initialSize=0
#定义最大连接数
maxActive=20
#定义最大空闲
maxIdle=20
#定义最小空闲
minIdle=1
#定义最长等待时间
maxWait=60000
```
2.写（jdbc.properties）
``` properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://192.168.8.20:3306/newagency?useUnicode=true&characterEncoding=UTF-8
username=***
password=***
#password=123
#定义初始连接数
initialSize=0
#定义最大连接数
maxActive=20
#定义最大空闲
maxIdle=20
#定义最小空闲
minIdle=1
#定义最长等待时间
maxWait=60000
```
xml文件需添加的相关配置
-----------------------------
1.spring-mybatis.xml
``` xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"
	   xmlns:context="http://www.springframework.org/schema/context"
	   xmlns:mvc="http://www.springframework.org/schema/mvc" xmlns:aop="http://www.springframework.org/schema/aop"
	   xsi:schemaLocation="http://www.springframework.org/schema/beans
                        http://www.springframework.org/schema/beans/spring-beans-3.1.xsd  
                        http://www.springframework.org/schema/context  
                        http://www.springframework.org/schema/context/spring-context-3.1.xsd
                        http://www.springframework.org/schema/mvc
                        http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd
                        http://www.springframework.org/schema/aop
							http://www.springframework.org/schema/aop/spring-aop.xsd ">
<bean id="masterdataSource" class="org.apache.commons.dbcp.BasicDataSource"
		destroy-method="close">
		<property name="driverClassName" value="${driver}" />
		<property name="url" value="${masterdataSourceurl}" />
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
		<!-- 初始化连接大小 -->
		<property name="initialSize" value="${initialSize}"></property>
		<!-- 连接池最大数量 -->
		<property name="maxActive" value="${maxActive}"></property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle" value="${maxIdle}"></property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle" value="${minIdle}"></property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait" value="${maxWait}"></property>

		<!-- removeAbandoned: 是否自动回收超时连接 -->
		<property name="removeAbandoned" value="${removeAbandoned}"></property>
		<!-- removeAbandonedTimeout: 超时时间(以秒数为单位) -->
		<property name="removeAbandonedTimeout" value="${removeAbandonedTimeout}"></property>
		<!-- 在空闲连接回收器线程运行期间休眠的时间值,以毫秒为单位 -->
		<property name="timeBetweenEvictionRunsMillis" value="${timeBetweenEvictionRunsMillis}"></property>
		<!-- 在每次空闲连接回收器线程(如果有)运行时检查的连接数量 -->
		<property name="numTestsPerEvictionRun" value="${numTestsPerEvictionRun}"></property>
		<!-- 1000 * 60 * 30  连接在池中保持空闲而不被空闲连接回收器线程 -->
		<property name="minEvictableIdleTimeMillis" value="${minEvictableIdleTimeMillis}"></property>
		<property name="validationQuery" value="${validationQuery}"></property>
		<!-- 定时对线程池中的链接进行validateObject校验，对无效的链接进行关闭 -->
		<!-- <property name="testWhileIdle" value="${testWhileIdle}"></property> -->
		<!-- 指定在从连接池中拿连接时，要检查连接是否有效，若无效会将连接从连接池中移除掉 -->
		<property name="testOnBorrow" value="${testOnBorrow}"></property>
	</bean>
	<bean id="slavedataSource" class="org.apache.commons.dbcp.BasicDataSource"
		  destroy-method="close">
		<property name="driverClassName" value="${driver}" />
		<property name="url" value="${slavedataSourceurl}" />
		<property name="username" value="${username}" />
		<property name="password" value="${password}" />
		<!-- 初始化连接大小 -->
		<property name="initialSize" value="${initialSize}"></property>
		<!-- 连接池最大数量 -->
		<property name="maxActive" value="${maxActive}"></property>
		<!-- 连接池最大空闲 -->
		<property name="maxIdle" value="${maxIdle}"></property>
		<!-- 连接池最小空闲 -->
		<property name="minIdle" value="${minIdle}"></property>
		<!-- 获取连接最大等待时间 -->
		<property name="maxWait" value="${maxWait}"></property>

		<!-- removeAbandoned: 是否自动回收超时连接 -->
		<property name="removeAbandoned" value="${removeAbandoned}"></property>
		<!-- removeAbandonedTimeout: 超时时间(以秒数为单位) -->
		<property name="removeAbandonedTimeout" value="${removeAbandonedTimeout}"></property>
		<!-- 在空闲连接回收器线程运行期间休眠的时间值,以毫秒为单位 -->
		<property name="timeBetweenEvictionRunsMillis" value="${timeBetweenEvictionRunsMillis}"></property>
		<!-- 在每次空闲连接回收器线程(如果有)运行时检查的连接数量 -->
		<property name="numTestsPerEvictionRun" value="${numTestsPerEvictionRun}"></property>
		<!-- 1000 * 60 * 30  连接在池中保持空闲而不被空闲连接回收器线程 -->
		<property name="minEvictableIdleTimeMillis" value="${minEvictableIdleTimeMillis}"></property>
		<property name="validationQuery" value="${validationQuery}"></property>
		<!-- 定时对线程池中的链接进行validateObject校验，对无效的链接进行关闭 -->
		<!-- <property name="testWhileIdle" value="${testWhileIdle}"></property> -->
		<!-- 指定在从连接池中拿连接时，要检查连接是否有效，若无效会将连接从连接池中移除掉 -->
		<property name="testOnBorrow" value="${testOnBorrow}"></property>
	</bean>

	<bean id="dataSource" class="com.cn.hjsj.base.SqlSource.DynamicDataSource">
		<property name="targetDataSources">
			<map key-type="java.lang.String">
				<!-- write -->
				<entry key="master" value-ref="masterdataSource"/>
				<!-- read -->
				<entry key="slave" value-ref="slavedataSource"/>
			</map>

		</property>
		<property name="defaultTargetDataSource" ref="masterdataSource"/>
	</bean>

  <!-- 配置数据库注解aop -->
	<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
	<bean id="manyDataSourceAspect" class="com.cn.hjsj.base.aop.DataSourceAspect" />
	<aop:config>
		<aop:aspect id="c" ref="manyDataSourceAspect">
			<aop:pointcut id="tx" expression="execution(* com.cn.hjsj.dao.*.*(..))"/>
			<aop:before pointcut-ref="tx" method="before"/>
		</aop:aspect>
	</aop:config>
	<!-- 配置数据库注解aop -->
```
Java源码
----------------------------
1.DataSource
``` java
/**
* Created by LT on 2017-07-31.
*/
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface DataSource {
  String value();
}

```
2.DataSourceAspect

```java
/**
 * Created by LT on 2017-07-31.
 */
public class DataSourceAspect {
    public void before(JoinPoint point)
    {
        Object target = point.getTarget();
        String method = point.getSignature().getName();

        Class<?>[] classz = target.getClass().getInterfaces();

        Class<?>[] parameterTypes = ((MethodSignature) point.getSignature())
                .getMethod().getParameterTypes();
        try {
            Method m = classz[0].getMethod(method, parameterTypes);
            if (m != null && m.isAnnotationPresent(DataSource.class)) {
                DataSource data = m
                        .getAnnotation(DataSource.class);
                DynamicDataSourceHolder.putDataSource(data.value());
                System.out.println("datasource come from:"+data.value());
            }

        } catch (Exception e) {
            // TODO: handle exception
        }
    }
}
```
3.DynamicDataSource
```java
/**
 * Created by LT on 2017-07-31.
 */
public class DynamicDataSource extends AbstractRoutingDataSource{
    @Override
    protected Object determineCurrentLookupKey() {
        return DynamicDataSourceHolder.getDataSouce();
    }
}
```
4.DynamicDataSourceHolder
```java
/**
 * Created by LT on 2017-07-31.
 */
public class DynamicDataSourceHolder {
    public static final ThreadLocal<String> holder = new ThreadLocal<String>();

    public static void putDataSource(String name) {
        holder.set(name);
    }

    public static String getDataSouce() {
        return holder.get();
    }
}
```
测试
-------------------------
1.mapper文件
```SqlSource
<select id="getListCount" resultType="java.lang.Integer">
        <![CDATA[
		SELECT count(*) FROM system_user
		]]>
    </select>
    <update id="userExit" parameterType="com.cn.hjsj.pojo.User">
        update system_user
        <set>
            recordStatus = #{recordStatus,jdbcType=VARCHAR},
        </set>
        where userId= #{userId,jdbcType=INTEGER}
    </update>
```
2.Dao层
```java
   @DataSource("master")
   public Integer getListCount();
   @DataSource("slave")
   public Integer userExit(User user);
```
3.Controller层
```java
     int i = testService.getListCount();
     int j = testService.userExit(user);
```
结果
-------------------------
![Mou_icon](http://ou38qmztk.bkt.clouddn.com/Test.bmp)
根据 DataSourceAspect.java 打印出来的结果实现了读写分离。
