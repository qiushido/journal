# SpringBoot 快速入门
> 分享学习过程，暴露问题，收集意见经验更快进步  
> 作者：杜秋实  




> #### 目录：
> + 什么是SpringBoot？  
> + 准备应用环境  
> + MVC及重要概念  
> + Mybatis持久层框架 
> + Thymeleaf模板引擎  
> + 源码地址 
> + 数不尽的坑  

## 什么是Spring Boot？  
SpringBoot是 Spring 家族中的一员  
Spring Boot 就是对Spring各个组件配置进行打包的一款应用程序，使用它可以快速启动新的Spring应用开发，减少手动配置，需要的仍然是Spring框架知识。  
Spring是一款松耦合的轻量级java应用开发框架。

<center>
![SpringBoot](http://www.yiibai.com/uploads/images/201701/03/557150107_51249.jpg)
</center>  

## 准备应用环境  
- java环境（7+）
- Maven  
- IDE  
- [Eclipse插件安装使用教程：STS (Spring Tool Suite)](http://blog.csdn.net/he90227/article/details/53308222)  
-  SpringBoot框架依赖打包：http://start.spring.io （打包时勾选DevTool，当使用Thymeleaf模板时可以即时调试）  
-  SpringBoot使用注解的方式逐渐取代XML配置文件   

## MVC及重要概念
- #### MVC (Model-View-Controller)  
	MVC是Web开发中的重要概念        
<center>  
![mvc图](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1521690343115&di=c81316deea32e6167de20c0b9255ff44&imgtype=0&src=http%3A%2F%2Fimages.cnitblog.com%2Fblog%2F191097%2F201307%2F17122850-e60fd55ef7ed48679f6785aa3ff80082.png)</center>  
	- Model（模型）是应用程序中用于处理应用程序数据逻辑的部分。通常模型对象负责在数据库中存取数据。  
	- View（视图）是应用程序中处理数据显示的部分。通常视图是依据模型数据创建的。
	- Controller（控制器）是应用程序中处理用户交互的部分。通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据。  
	- 依据MVC设计框架解耦功能，让设计系统的结构、功能更清晰，更容易扩展及重用。掌握mvc框架模式我认为可以更容易写出好维护的大型项目，MVC的概念掌握好以后，可以在不同语言不同框架中轻松游走。
- #### 重要概念：AOP面向切面编程  
	- MVC是基本模式，在SpringBoot框架中除了M-V-C层以为，还有服务层、安全层、工具层等，在多个服务层中抽取公共类似的功能点，作为切点，将剥离的切面服务从合适的时机引入服务层。简单描述就是切面层可以服务于多个服务层。可以实现多切面，多服务层。拦截器、过滤器的应用基于AOP思想。  
  <center>![AOP](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1521690410529&di=fc7521c693c834b07cd93e6e12ba5b5b&imgtype=0&src=http%3A%2F%2Fimages2015.cnblogs.com%2Fblog%2F468725%2F201508%2F468725-20150831100452169-1977975177.png)  </center>
    
- #### 重要概念：IOC控制反转 和 DI依赖注入  
<center>![IOC](https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=574837388,2822180527&fm=27&gp=0.jpg)</center>
	- 基于java反射原理实现，将创建对象的权利交给框架。举一个网上易懂的例子：如果一个人想找一个合适的伴侣，可能会自己去网上查异性的资料，搜集信息，自己梳理成为另一半的要求、安排见面等等，这种操作是直接联系的，想找伴侣就要自己设计要求，自己选找可能的对象，自己去判断每一个可能的人。而如果通过婚姻介绍所，婚姻介绍所里注册了很多有需求的人，当一个人想要寻找合适的伴侣的时候只需要提出自己的要求，婚介所按照要求去寻找合适的对象来安排，这样婚介所接管了寻找对象的权利，而婚介所就相当于spring框架，在有需要的时候创建所需要的方法，提供使用，此即为IOC控制反转。spring提供所需要的对象的时候所用的手段即为DI，依赖注入。
- ##### Model模型层  
	- 模型，是对于数据操作而言的，程序服务势必需要数据操作。通过建立数据模型来实现对数据库中数据的对应，模型对象的建立需要模型类的字段与数据表中的字段一致。
	
- ##### DAO(DataAccessObject数据访问对象)数据持久化层  
	- 用@Mapper注解的一层，访问数据库的接口层，通过dao层访问数据库，将访问返回的数据放入数据Model中，实现从sql到服务的调用。Model和DA0同属于数据处理层，Mybatis持久层框架，简化了数据操作，尽量的避免了JDBC的代码和很多的参数设置，每个MyBatis应用程序主要都是使用SqlSessionFactory实例的，一个SqlSessionFactory实例可以通过SqlSessionFactoryBuilder获得。SqlSessionFactoryBuilder可以从一个xml配置文件或者一个预定义的配置类的实例获得。通过在接口方法上使用sql注解的方式，已经可以省去xml映射文件，和SqlSessionFactory的构建过程。  

- ##### Controller控制层  
	- @Controller注解的控制层，*`控制web访问的路由信息`*，根据controller设定的路由访问服务会返回特定的方法，调用特定的服务。Http请求响应参数在这一层与前端页面传入结合，通过模板引擎获取用户输入的数据与返回用户请求的数据,*`传递接收参数`*。  
	- @RestController和@Controller的区别？  
@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。@ResponseBody返回类型为json。如果需要渲染前端页面要用@Controller。  
	- @RequestMapping("/")  
	- @GetMapping("/")
	- @PostMapping("/")    

- ##### View视图层  
	- 使用Thymeleaf模板渲染引擎，resources中的static和templates中的页面是提供的渲染模板。至于如何有好的视图效果要深入Thymeleaf模板学习才知道。  
	- 视图的渲染基本逃不开数据参数，参数的传递都是通过controller控制层，通过对象的形式传入传出参数。  
- ##### Services服务层  
	- 提供具体业务逻辑的处理办法，包含两部分具体业务的服务接口和接口的实现
- ##### Interceptor拦截器  
	- 拦截器，相当于一个切面，对于一些注册登录相关的权限管理需要拦截器来过滤一些请求访问，简单的拦截器分为两部分，一为拦截方法，二为拦截器策略  

``` java  
//拦截器方法
public class BaseInterceptor implements HandlerInterceptor {
    //Logger logger = LoggerFactory.getLogger(BaseInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {
            
   }
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
            ModelAndView modelAndView) throws Exception {
        // TODO Auto-generated method stub

    }
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)
            throws Exception {
        // TODO Auto-generated method stub

    }
```  

```  java  
//拦截器配置策略

@Configuration
public class WebConfig  extends WebMvcConfigurerAdapter  {
	
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
		super.addResourceHandlers(registry);
	}
	
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new BaseInterceptor()).addPathPatterns("/admin/**","/User/**")
														.excludePathPatterns("/");
		super.addInterceptors(registry);
	}
}
```  


- ##### resources中的application.properties  
	- 一些配置可以在里面加如springMVC的web访问等，不局限于此，可用时添加  

```  
//mysql和thymeleaf的配置文件
# thymeleaf 
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html  
spring.thymeleaf.mode=HTML5  
spring.thymeleaf.encoding=UTF-8  
spring.thymeleaf.content-type=text/html    
spring.thymeleaf.cache=false 
#Config MySQL Connect Info
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/UserDB?useSSL=false
spring.datasource.username=root
spring.datasource.password=xuexi123
spring.datasource.driver-class-name=com.mysql.jdbc.Driver  
```  

- ##### 资源依赖pom.xml  
maven自动检查此文件中的加入的依赖信息，自动拉取jar包到项目中  
- ##### 简单工程的文件结构  
<center>![简单工程的文件结构](http://www.qiushi.today/img/fileFramework.png)</center>
## Thymeleaf模板引擎  
> 模板引擎的作用？  
> 可以帮助程序实现用户界面与业务数据分离，业务代码与逻辑代码的分离，提升开发效率，还利于代码重用。  
> 在反馈前端页面的时候将数据模型应用到视图渲染  

    模板引入配置:  
    1.application.properties文件配置  
    2.@controller控制器设置  
    3.模板html页面放在/src/resources/templates/  
    4.静态资源放于/src/resources/static/
- application.properties配置文件
```  
    spring.thymeleaf.prefix=classpath:/templates/（位于/src/resources/下面）
    spring.thymeleaf.suffix=.html  
    spring.thymeleaf.mode=HTML5  
    spring.thymeleaf.encoding=UTF-8  
    spring.thymeleaf.content-type=text/html    
    spring.thymeleaf.cache=false 
```  
+ 模板使用中可能遇到的问题  
 - Mapping for /error:  
        This application has no explicit mapping for /error, so you are seeing this  
  文件层次结构问题  
``` 
application启动类需要和其他层至于同一目录
即application启动类/Models/Service/dao/Controller同一级
``` 


 ## Mybatis 数据库操作　　
#### 什么是 MyBatis ？
> MyBatis 是一款优秀的*持久层框架*，它支持定制化 SQL、存储过程以及高级映射。MyBatis *避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集*。  
> MyBatis 可以使用简单的*` XML`* 或*`注解`*来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。  

##### Mybatis主要涉及到两层结构
 - DAO层：
 	- interface接口层，配合数据库操作，提供数据库CRUD操作接口  
 	使用@Mapper注解。不使用可能会遇到如下启动错误：  
```
Description:  
    Field userMapper in com.UnionPay.UserManageSys.Controller.UserController required a bean of type 'com.UnionPay.UserManageSys.dao.UserMapper' that could not be found.
    Action:  
    Consider defining a bean of type 'com.UnionPay.UserManageSys.dao.UserMapper' in your configuration.
```

 - Model层： 	
 	- XML映射文件：（如果在Mapper中配置方法，简单方法可省略此文件）  
 	取代XML方法，在Mapper注解的数据库操作接口中，使用注解方式注解具体操作方法。
 	- model：model类中的字段名需要和SQL表中字段相一致
 - configure配置方法有二：
- application.prepties文件添加（推荐）
```  
#Config MySQL Connect Info
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/UserDB
spring.datasource.username=root
spring.datasource.password=xuexi123
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
``` 
- （不推荐） config.xml：放置于src/config/Configure.xml 
 	配置文件如下：
    
 ``` 
 <?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <typeAliases>
    //！对应模型的具体名称，需要和xml映射文件相同
        <typeAlias alias="User" type="com.yiibai.mybatis.models.User" />
    </typeAliases>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC" />
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver" />
                <property name="url" value="jdbc:mysql://127.0.0.1:3306/testdb" />
                <property name="username" value="root" />
                <property name="password" value="123456" />
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <!-- xml 数据表与操作映射文件-->
        <mapper resource="com/yiibai/mybatis/models/User.xml" />
    </mappers>
</configuration>
```　　
#### 与原生建立映射的两种配置方式：　　
+ XML配置方式：
```  xml
<mapper namespace="com.UnionPay.Models.UserMapper">
		<select id="GetUserByID" parameterType="int" resultType="User">
			select * from `user` where user_id = #{user_id}
    	</select><select id="GetUserByName" parameterType="String" resultType="User">
    		select * from `user` where user_name = #{user_name}
    	</select>
     </mapper>
```  
+ 注解方式：  
```  java
@Mapper
public interface UserMapper {
	//获取用户模型的方法，通过用户名
	//!!! 是 `user` 不是 'user'
	@Select("select * from `user` where user_name = #{userName}")
	public User getUserByName(String userName);
```  

#### 多参数传入方法：
+ DAO层函数方法：使用@Param()注解  
``` 
	@Insert("insert into `user`(user_name,user_pwd,user_authority) values (#{user_name},#{user_pwd},#{user_authority})")
	public int addUser(@Param("user_name")String user_name,@Param("user_pwd")String user_pwd,@Param("user_authority")int user_authority)
``` 
---
## SpringBoot 实例逻辑

#### 基本的逻辑流程　　
<center>
![实例逻辑](http://www.qiushi.today/img/liucheng.png)  
</center>  

[实例展示](http://localhost:8080/)

## 源码地址

源码地址：可以直接git clone   https://gitee.com/unionpay_quickpass/UserManagerSys.git  

## 数不尽的坑  
+ Model层中不可一而再的坑  
> Model类中的字段要和数据表中的字段一一对应，必须一字不差
+ DAO层语句中的千差万别  
> `` `user` `` 和 'user'的区别
```  java
	@Select("select * from `user` where user_name = #{userName}")
	public User getUserByName(String userName);
```  
+ 看似表面都是字符串的比较  
> 基础问题。equal方法 和 == 的差别。框架中使用String对比功能，尽量使用equal方法。这里如果产生bug，将会隐藏很深。  

等等  



