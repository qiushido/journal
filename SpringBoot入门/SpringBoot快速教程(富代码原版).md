# SpringBoot 框架快速入门
	
* #####  准备环境（依次）：
	- java环境配置（7+）
	- Maven安装
	- Eclipse 
	- [Eclipse插件安装使用教程：STS (Spring Tool Suite)](http://blog.csdn.net/he90227/article/details/53308222)
	-  SpringBoot框架依赖打包：www.start.spring.io （打包时勾选DevTool，当使用Thymeleaf模板时可以即时调试）
	-  SpringBoot使用注解的方式逐渐取代XML配置文件 
* ##### 最终实现的系统：  
	基于SpringBoot + Mybatis + Thymeleaf + Mysql  
    实现用户权限管理系统，普通用户登录成功后只有注销功能，管理员登陆后可以实现用户增加/删除/修改权限/查询用户列表的功能
---  
## Spring MVC 框架  
- 重要概念：MVC(Model-View-Controller)  
	- Model（模型）是应用程序中用于处理应用程序数据逻辑的部分。通常模型对象负责在数据库中存取数据。  
	- View（视图）是应用程序中处理数据显示的部分。通常视图是依据模型数据创建的。
	- Controller（控制器）是应用程序中处理用户交互的部分。通常控制器负责从视图读取数据，控制用户输入，并向模型发送数据。  
	- 依据MVC设计框架解耦功能，让设计系统的结构、功能更清晰，更容易扩展及重用。掌握mvc框架模式我认为可以更容易写出好维护的大型项目，MVC的概念掌握好以后，可以在不同语言不同框架中轻松游走。
- 重要概念：AOP面向切面编程  
	- MVC是基本模式，在SpringBoot框架中除了M-V-C层以为，还有服务层、安全层、工具层等，在多个服务层中抽取公共类似的功能点，作为切点，将剥离的切面服务从合适的时机引入服务层。简单描述就是切面层可以服务于多个服务层。可以实现多切面，多服务层。拦截器、过滤器的应用基于AOP思想。
- 重要概念：IOC控制反转 和 DI依赖注入  
	- 基于java反射原理实现，将创建对象的权利交给框架。举一个网上易懂的例子：如果一个人想找一个合适的伴侣，可能会自己去网上查异性的资料，搜集信息，自己梳理成为另一半的要求、安排见面等等，这种操作是直接联系的，想找伴侣就要自己设计要求，自己选找可能的对象，自己去判断每一个可能的人。而如果通过婚姻介绍所，婚姻介绍所里注册了很多有需求的人，当一个人想要寻找合适的伴侣的时候只需要提出自己的要求，婚介所按照要求去寻找合适的对象来安排，这样婚介所接管了寻找对象的权利，而婚介所就相当于spring框架，在有需要的时候创建所需要的方法，提供使用，此即为IOC控制反转。spring提供所需要的对象的时候所用的手段即为DI，依赖注入。
- Model模型层  
	- 模型，是对于数据操作而言的，程序服务势必需要数据操作。通过建立数据模型来实现对数据库中数据的对应，模型对象的建立需要模型类的字段与数据表中的字段一致。
- DAO(DataAccessObject数据访问对象)数据持久化层  
	- 用@Mapper注解的一层，访问数据库的接口层，通过dao层访问数据库，将访问返回的数据放入数据Model中，实现从sql到服务的调用。Model和DA0同属于数据处理层，Mybatis持久层框架，简化了数据操作，尽量的避免了JDBC的代码和很多的参数设置，每个MyBatis应用程序主要都是使用SqlSessionFactory实例的，一个SqlSessionFactory实例可以通过SqlSessionFactoryBuilder获得。SqlSessionFactoryBuilder可以从一个xml配置文件或者一个预定义的配置类的实例获得。通过在接口方法上使用sql注解的方式，已经可以省去xml映射文件，和SqlSessionFactory的构建过程。  
	- 如下有一个使用SqlSessionFactory的代码案例，在项目目录中新建与启动类平级别的config文件夹，其中加入configure.xml,这个配置文件中配置mapper的路径，即数据库具体操作映射。
```  
// sql操作接口案例
public interface SqlService {
	private static SqlSessionFactory sqlSessionFactory;
	private static Reader reader;

	static {
		try {
			reader = Resources.getResourceAsReader("com/xxx/config/Configure.xml");
			sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
			//脱离模型映射文件以后新加入配置文件
			sqlSessionFactory.getConfiguration().addMapper(UserMapper.class);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	public static SqlSessionFactory getSession() {
		return sqlSessionFactory;
	}

	//用户查询
	public User userSelect(String userName) {
		SqlSession session = sqlSessionFactory.openSession();
		UserMapper userMapper = session.getMapper(UserMapper.class);
		User user = userMapper.getUser(userName);
		System.out.println("select "+ userName +"ok!");
		session.close();
		return user;
	}
	//用户增加
	public static void userAdd(String userName,String password,int authority) {

		SqlSession session = sqlSessionFactory.openSession();
		try {
			UserMapper userMapper = session.getMapper(UserMapper.class);
			int flag = userMapper.addUser(userName,password,authority);
			if(flag == 0) {
				System.out.println("Add ok!");
			}
			}catch(Exception e){
				e.printStackTrace();
			}finally {				
				session.commit();
				session.close();
			}
	}
	//用户权限更改
	public static void userChg(int newAuthority) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			
		}catch(Exception e){
			e.printStackTrace();
		}finally {
			System.out.println("update ok!");
			session.commit();
			session.close();
		}
		
	}
	//用户删除
	public static void userDel(String userName) {
		SqlSession session = sqlSessionFactory.openSession();
		try {
			
		}catch(Exception e){
			e.printStackTrace();
		}finally {
			System.out.println("delete ok!");
			session.commit();
			session.close();
		}
		
	}
```    
```  
// configure.xml示例  
?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<typeAliases>
		<typeAlias alias="User" type="com.xx项目名xxx.Models.User" />
	</typeAliases>
	<environments default="development">
	    <environment id="development">
		<transactionManager type="JDBC" />
			<dataSource type="POOLED">  
                <property name="driver" value="com.mysql.jdbc.Driver" />
			    <property name="url" value="jdbc:mysql://127.0.0.1:3306/UserDB" />
			    <property name="username" value="root" />
			    <property name="password" value="xuexi123" />
             </dataSource>
		</environment>
	</environments>

	<mappers>
	     <!-- 数据库操作映射xml路径-->
	     	<mapper resource="com/xxxx/Models/User.xml" />

	</mappers>
</configuration>
```  

```  
	<!-- 简单的数mapper操作映射，其实就是接口的实现-->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- UserMapper定义了具体的数据操作方法访问类，id为方法名称，parameterType为传入参数类型，resultType为返回类型-->
<mapper namespace="com.xxx.Models.UserMapper">
	<select id="GetUserByID" parameterType="int" resultType="User">
		select * from `user` where user_id = #{user_id}
    </select>
    <select id="GetUserByName" parameterType="String" resultType="User">
    	select * from `user` where user_name = #{user_name}
    </select>
</mapper>
```  

- Controller控制层  
	- @Controller注解的控制层，控制web访问的路由信息，根据controller设定的路由访问服务会返回特定的方法，调用特定的服务。Http请求响应参数在这一层与前端页面传入结合，通过模板引擎获取用户输入的数据与返回用户请求的数据。  
	- @RestController和@Controller的区别？  
@RestController注解相当于@ResponseBody ＋ @Controller合在一起的作用。@ResponseBody返回类型为json。如果需要渲染前端页面要用@Controller。  
	- @RequestMapping("/")  
	- @GetMapping("/")
	- @PostMapping("/")  
	- 两种方式代码示例：  
```  
//Model参数的传入是模板渲染的参数。@RequestParam注解为页面传回参数的参数  
 @RequestMapping(value="/")
public String index(Model model,@RequestParam(value="userName",defaultValue="")String nameStr,
								@RequestParam(value="userPwd",defaultValue="")String pwdStr){ 
	try {
		User user = userLoginService.getUserByName(nameStr);
		if(user != null ) {
			if(user.getUser_pwd().equals(pwdStr)) {
				return "index";
			}
		}
	}catch(Exception e){
		e.printStackTrace();
	}finally {
		System.out.println("login success");
	}
	return "index";
```  
```  
	@Autowired
	private IuserService userLoginService;
	
    //页面生成处理方法
	@GetMapping(value= {"/base","/"})
	public String index(Model model,HttpServletRequest request) {
		model.addAttribute("user",new User());
		return "base";
	}
    //参数返回处理方法
	@PostMapping(value= {"/base","/"})
	public String index(@ModelAttribute User user,HttpServletRequest request) {
		
		User userModel = userLoginService.getUserByName(user.getUser_name());
		if(userModel == null) {
			return "base";
		}
		request.getSession().setAttribute("userId", userModel.getUser_name());
		request.getSession().setAttribute("userPwd", userModel.getUser_pwd());
		if(userModel.getUser_pwd().equals(user.getUser_pwd()) && (userModel.getUser_authority() == 1)) {
				return "redirect:/admin";
		}else if(userModel.getUser_pwd().equals(user.getUser_pwd()) && (userModel.getUser_authority() != 1)){
			return "redirect:/User";
		}else {
			return "base";
		}
	}
```  

- View视图层  
	- 使用Thymeleaf模板渲染引擎，resources中的static和templates中的页面是提供的渲染模板。至于如何有好的视图效果要深入Thymeleaf模板渲染的使用。
- Services服务层  
	- 提供具体业务逻辑的处理办法，包含两部分具体业务的服务接口和接口的实现
- Interceptor拦截器  
	- 拦截器，相当于一个切面，对于一些注册登录相关的权限管理需要拦截器来过滤一些请求访问，下面时拦截器简单应用示例：拦截器分为两部分，一为拦截方法，二位拦截器配置  
```  
//拦截器
public class BaseInterceptor implements HandlerInterceptor {
    //Logger logger = LoggerFactory.getLogger(BaseInterceptor.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
            throws Exception {

        //获取session
        HttpSession session = request.getSession(true);
        //判断用户ID是否存在，不存在就跳转到登录界面
        if(session.getAttribute("userId") == null){
       //     logger.info("------:跳转到login页面！");
        	response.sendRedirect("/");
            return false;
        }else{
            session.setAttribute("userId", session.getAttribute("userId"));
            return true;
        }
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
```  
//拦截器配置
@Configuration
public class WebConfig  extends WebMvcConfigurerAdapter  {
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {	registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
		super.addResourceHandlers(registry);
	}
    //拦截访问路径和排除的访问路径
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		registry.addInterceptor(new BaseInterceptor()).addPathPatterns("/admin/**","/User/**")
														.excludePathPatterns("/");
		super.addInterceptors(registry);
	}
}
```  

- resources中的application.properties  
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

- 资源依赖pom.xml  
maven自动检查此文件中的加入的依赖信息，自动拉取jar包到项目中
## Thymeleaf模板引擎  
	模板引入配置:  
    1.application.properties文件配置  
    2.@controller控制器设置  
- application.properties配置文件
```  
    spring.thymeleaf.prefix=classpath:/templates/（位于/src/resources/下面）
    spring.thymeleaf.suffix=.html  
    spring.thymeleaf.mode=HTML5  
    spring.thymeleaf.encoding=UTF-8  
    spring.thymeleaf.content-type=text/html    
    spring.thymeleaf.cache=false 
```  
### 可能的问题  
 - Mapping for /error:  
        This application has no explicit mapping for /error, so you are seeing this  
  文件层次结构问题  
``` 
application启动类需要和其他层至于同一目录
即application启动类/Models/Service/dao/Controller同一级
``` 

---  
 ## Mybatis 数据库操作
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
 	- XML映射文件：（此为如在配置方法，简单方法可省略此文件）  
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
        <!-- // power by http://www.yiibai.com -->
        <mapper resource="com/yiibai/mybatis/models/User.xml" />
    </mappers>
</configuration>
```
#### 多参数传入方法：
+ DAO层函数方法：使用@Param()注解  
``` 
	@Insert("insert into `user`(user_name,user_pwd,user_authority) values (#{user_name},#{user_pwd},#{user_authority})")
	public int addUser(@Param("user_name")String user_name,@Param("user_pwd")String user_pwd,@Param("user_authority")int user_authority)
``` 


---
## 可能遇到的问题集合：
- 启动：Run As ->Spring Boot App  
- 调试过程中：
	- 修改代码Relaunch重启以后内容不会改变？  
	未安装DevTool插件   
    
    
 - 依赖相关：
 	- Maven本地依赖：mvn dependency:tree 安装检查依赖  
 	- pom.xml为依赖添加文件
 - string量的比较：equals()方法和==的区别  
 - post get put 区别
 - Mapper层中，表的输入`user`与'user' 的区别！