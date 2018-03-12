# SpringBoot 框架快速入门
	
* #####  准备环境（依次）：
	- java环境配置（7+）
	- Maven安装
	- Eclipse 
	- [Eclipse插件安装使用教程：STS (Spring Tool Suite)](http://blog.csdn.net/he90227/article/details/53308222)
	-  SpringBoot框架依赖打包：www.start.spring.io （打包时勾选DevTool，当使用Thymeleaf模板时可以即时调试） 

 
 ### 可能遇到的问题集合：
- 启动：Run As ->Spring Boot App  
- 调试过程中：
	- 修改代码Relaunch重启以后内容不会改变？  
	未安装DevTool插件   
    
    
 - 依赖相关：
 	- Maven本地依赖：mvn dependency:tree 安装检查依赖  
 	- pom.xml为依赖添加文件