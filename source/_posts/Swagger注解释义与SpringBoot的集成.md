---
title: Swagger注解释义与SpringBoot的集成
tags: [Java, SpringBoot]
date:  2018/07/28 17:02
categories: Java教程
---
<!-- 展示图片 -->
![Mou_icon](http://blog.lutao1726.top/swagger-001.png)
<!-- more -->

#### 一、Swagger基础介绍
##### 1.什么是Swagger
描述REST API格式的一组规则（规范），OpenAPI（几十个知名互联网公司定制的API文档规范，Swagger就是其实现之一）的前身。Swagger让测试人员和开发人员之间文档共享。用于前后端分离，接口管理和测试工具集。 
##### 2.能做什么
2.1.接口的文档在线自动生成
2.2.功能测试    
##### 3.版本    
3.1.Swagger1(很少用）
3.2.Swagger2（常用）
3.3.springfox-swagger2（用的最多）
###### 三者关系：
Swagger2是Swagger1的升级版。spring公司把swagger集成到自己的项目里，整了一个spring-swagger，后来便演变成springfox。springfox本身只是利用自身的aop的特点，通过plug的方式把swagger集成了进来，它本身对业务api的生成，还是依靠swagger来实现。
##### 4.Swagger主要工具   
![Mou_icon](http://blog.lutao1726.top/swagger-002.png)
**Swagger编辑器**：一个基于浏览器的编辑器，您可以在其中编写OpenAPI规范。
**Swagger-core**: 用于Java/Scala的的Swagger实现。与JAX-RS(Jersey、Resteasy、CXF...)、Servlets和Play框架进行集成。
**Swagger-ui**：一个无依赖的HTML、JS和CSS集合，可以为Swagger兼容API动态生成优雅文档。
**Swagger-codegen**：一个模板驱动引擎，通过分析用户Swagger资源声明以各种语言生成客户端代码。    

#### 二、注解说明
| 注解       | 使用位置  | 取值   | 说明   |
| --------   | :-----   | :----: |  :---- |
|  @Api                |用于Controller类上                |        |协议集描述        |  
|  @ApiModel           |用在返回对象类上                  |        | 对返回对象的描述       |  
|  @ApiModelProperty   |用在出入参数对象的字段上           |        | 描述对象属性       |  
|  @ApiImplicitParam   |用在controller的方法上            |        | 对API操作中的单个参数进行定义      |  
|  @ApiImplicitParams  |用在@ApiImplicitParams的方法里边  |        | 允许多个ApiImplicitParam对象列表的包装器，参看@ApiImplicitParam注解       |  
|  @ApiOperation       |用在controller的方法上            |        | 用来描述请求参数的作用       |  
|  @ApiParam           |用在Controller类的方法上          |        |  用来描述方法的作用                      |  
|  @ApiResponse        |用在@ApiResponses里边             |        |  返回单个结果集(类)说明     |  
|  @ApiResponses       |用在controller的方法上            |        |  返回结果集说明，参考@ApiResponse      |  
|  @Authorization      |用在controller的方法上            |        |  定义用于资源或操作的授权方案      |  
|  @AuthorizationScope |用在controller的方法上            |        |  用于定义为已定义授权方案的操作所使用的授权范围      |  
|  @ResponseHeader     |用在controller的方法上            |        |  响应可能提供的响应头列表     |  

**@Api**

| 属性       | 说明  |
| --------   | :-----   |
|value	     |    url的路径位置|
|tags	      |   设置该值，value值被覆盖|
|description	|     对api资源的描述(过时)|
|basePath	 |    基本路径可以不配置(过时)|
|position	  |   如果配置了多个Api，可用该注解改变显示顺序(过时)|
|produces	  |   For example, "application/json, application/xml"|
|consumes	  |   For example, "application/json, application/xml"|
|protocols	   |  Possible values: http, https, ws, wss|
|authorizations	| 高级特性认证时配置|
|hidden	       |  配置为true 将在文档中隐藏|
**@ApiModel**

| 属性       | 说明  |
| --------   | :-----   |
|value	      |  为模型提供一个替代名称，默认使用类名|
|description	 |   提供一个更长的类描述|
|parent	      |  为模型提供一个超类以允许描述继承|
|discriminator|	支持模型继承和多态性，这是用作鉴别器的字段的名称，基于这个字段，可以断言需要使用哪个子类型|
|subTypes	  |  继承自此模型的子类型的数组|
|reference	   | 指定对相应类型定义的引用，覆盖指定的任何其他元数据|
**@ApiModelProperty**

| 属性       | 说明  |
| --------   | :-----   |
|value	     |       属性的简要描述|
|name	        |    允许重写属性的名称|
|access	         |   允许从API文档中过滤参数|
|allowableValues	  |  限制此属性的可接受值|
|notes	          |  目前未使用|
|dataType	      |  参数的数据类型，这可以是类名或基元。该值将覆盖从类属性读取的数据类型|
|required	      |  指定是否需要参数，默认false|
|position	    |    允许显式地对模型中的属性进行排序|
|hidden	         |   允许模型属性隐藏在Swagger模型定义中|
|example	         |   属性的示例值|
|readOnly	      |  允许模型属性被指定为只读|
|reference	      |  指定对相应类型定义的引用，覆盖指定的任何其他元数据|
|allowEmptyValue	   | 允许传递空值|
**@ApiImplicitParam**

| 属性       | 说明  |
| --------   | :-----   |
|name	      |      参数的名称|
|value	       |     参数的简要描述|
|defaultValue	|    描述参数的默认值|
|allowableValues	 |   限制此参数的可接受值|
|required	    |    指定是否需要参数，默认false|
|access	         |   允许从API文档中过滤参数|
|allowMultiple	|    指定参数是否可以接受多个逗号分隔的值|
|dataType	     |   参数的数据类型，可以是类名或基元|
|dataTypeClass	|    url的路径位置|
|paramType	     |   参数的参数类型，有效值是路径、查询、正文、标题或表单|
|example	         |   非主体类型参数的一个示例|
|examples	     |   参数示例。仅适用于body参数|
|type	         |   添加覆盖检测到的类型的能力|
|format	          |  添加提供自定义格式的功能|
|format	          |  添加提供自定义格式的功能|
|allowEmptyValue	   | 添加将格式设置为空的功能|
|collectionFormat    |添加使用' array '类型覆盖collectionFormat的功能|


**@ApiOperation**

| 属性       | 说明  |
| --------   | :-----   |
|value	          |  操作的简要描述|
|notes	          |  操作的详细描述|
|tags	          |  标记可用于根据资源或任何其他限定符对操作进行逻辑分组，会覆盖value值|
|response	      |  操作的响应类型|
|responseContainer	|声明一个容器，有效值是“List”、“Set”或“Map”。其他任何值都将被忽略|
|responseReference	|指定对响应类型的引用，覆盖任何指定的response()类|
|httpMethod	       | 指定一种请求方式|
|position	     |   (过时)|
|nickname	     |   第三方工具使用operationId来惟一地标识该操作|
|produces	    |    对应于操作的“生成”字段，接受内容类型的逗号分隔值。例如，“application/json, application/xml”将建议此操作生成json和xml输出。|
|consumes	     |   接受内容类型的逗号分隔值。例如，“application/json, application/xml”将建议此API资源接受json和xml输入|
|protocols	     |   为该操作设置特定的协议(方案)，可用协议的逗号分隔值。可能的值:http、https、ws、wss。|
|authorizations	  |  获取此操作的授权(安全需求)列表|
|hidden	         |   从操作列表中隐藏操作|
|responseHeaders	  |  响应可能提供的响应头列表|
|code	          |  响应状态码，默认200|
|extensions	      |  可选的扩展数组|
**@ApiParam**

| 属性       | 说明  |
| --------   | :-----   |
|name	         |   参数名称|
|value	          |  参数简要描述|
|defaultValue	  |  参数的默认值|
|allowableValues	   | 限制可接受参数|
|required	       | 指定是否需要参数，默认false|
|access	          |  允许从API文档中过滤参数|
|allowMultiple	  |  指定参数是否可以通过多次出现来接受多个值|
|hidden	          |  从参数列表中隐藏参数|
|example	          |  非主体类型参数的一个示例|
|examples	       | 参数示例。仅适用于body参数|
|type	         |   添加覆盖检测到的类型的能力|
|format	          |  添加提供自定义格式的功能|
|allowEmptyValue	   | 添加将格式设置为empty的功能|
|readOnly	       | 增加被指定为只读的能力|
|collectionFormat	|添加使用' array '类型覆盖collectionFormat的功能|
**@ApiResponse**

| 属性       | 说明  |
| --------   | :-----   |
|code|	            状态码，默认200|
|message|	            返回响应信息|
|response|	        可选的响应类来描述消息的有效负载|
|reference|	        指定对响应类型的引用，覆盖任何指定的response()类|
|responseHeaders|	    响应可能提供的响应头列表|
|responseContainer|	声明一个响应容器，有效值是“List”、“Set”或“Map”。其他任何值都将被忽略|


**@Authorization**

| 属性       | 说明  |
| --------   | :-----   |
|value|	用于此资源/操作的授权方案的名称|
|scopes|	如果授权模式是OAuth2，则使用的作用域|
**@AuthorizationScope**

| 属性       | 说明  |
| --------   | :-----   |
|scope	    |使用OAuth2授权方案的范围，作用域应该在Swagger对象的securityDefinition部分中预先声明|
|description	|用于遗留支持|
**@ResponseHeader**

| 属性       | 说明  |
| --------   | :-----   |
|name	     |       响应头的名字|
|description|	        响应头的长描述|
|response	      |  响应头的数据类型|
|responseContainer	|声明一个响应容器，有效值是“List”、“Set”或“Map”。其他任何值都将被忽略|

#### 三、与springBoot的集成
##### 3.1.引入jar包
```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.8.0</version>
</dependency>
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.8.0</version>
</dependency>
```
##### 3.2.创建Swagger配置类
```java
@Configuration//配置类
@EnableSwagger2//启用Swagger
public class SwaggerConfig {
    @Bean//加入到spring的容器中
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .pathMapping("/")
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.ergou.springswagger.controller"))//需要扫描的包路径
                .paths(PathSelectors.any())
                .build()
                .apiInfo(testApiInfo());
    }

    private ApiInfo testApiInfo() {
        return new ApiInfoBuilder()
                .title("springBoot集成swagger构建api文档")//标题
                .description("详细描述")//详细描述
                .version("1.0")//版本
                .termsOfServiceUrl("服务地址")
                .contact(new Contact("123","https://www.jianshu.com/","123@163.com"))//作者的一些信息
                .license("The Apache License, Version 2.0")//发布遵循协议
                .licenseUrl("http://www.apache.org/licenses/LICENSE-2.0.html")//协议地址
                .build();
    }
}
```    

##### 3.3.测试
在浏览器中输入：http://localhost:8080/swagger-ui.html，端口号改成自己配置的。
![Mou_icon](http://blog.lutao1726.top/swagger-003.png)



[详情](https://doc.xiaominfo.com/guide/)