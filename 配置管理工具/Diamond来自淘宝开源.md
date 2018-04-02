# 分布式配置管理工具对比分析  

### Diamond 淘宝开源 
> Diamond是一个持久配置管理中心，核心功能是使应用在运行中感知配置数据的变化，基于推送-订阅模式。  
> - Spring 3.x写的java应用，对外提供接口基于http协议  
> - 数据模型：key-value  
> 可以划分为 聚合和非聚合 性数据  
> 订阅者根据key获取value  
> 多个推送者以相同key推送数据，如果数据是聚合的，会合并在一起，非聚合的就会覆盖。  
- 淘宝Diamond之客户端架构解析  
https://www.jianshu.com/p/12f3b2d08478  
http://codemacro.com/2014/10/12/diamond/  
http://www.mamicode.com/info-detail-1283470.html  
https://blog.csdn.net/szwandcj/article/details/51165954  


#### 数据提交方式　　

> 数据的来源可能是人工通过管理端录入，也可能是其他服务通过配置管理服务的推送接口自动录入。


#### Diamond 架构  
Diamond服务是一个集群，是一个去除了单点的协作集群  
会有一个httpServer端存放所有的client和server主机列表  
任何一个实例client变更数据以后会提交到diamond服务端和mysql进行持久化配置，并通知其他所有实例在mysql上进行一次数据拉取，只拉取改变了的值。  
每一个实例会以一定的时间间隔从mysql进行一次全量拉取。
#### 架构  
> Diamond主要有ops, sdk, client和server 4个组件。  
> - Ops是运维用的配置工具，主要用于下发以及查询配置等
> - Server则是Diamond的后台，处理配置的一些逻辑；  
> - Sdk则是提供给ops或者其他第三方应用的开发工具包；sdk是用于构建前台运维配置程序的，本质是对数据的维护，所有的访问和操作都是直接面向数据库的；  
> - Client是编程API，diamond的消费者们（各子系统）都是通过client组件对server访问。  

<center>![jiagou](https://img-blog.csdn.net/20160415234747039)</center>  

#### 详细架构　　
<center>![具体执行架构](https://upload-images.jianshu.io/upload_images/1049928-ff5d4719450a4a9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)</center>  

#### 数据一致性  


#### 容灾  
每个实例有本地化文件，服务端也有本地文件，mysql也有同样的文件，实例可以以任何一方的配置文件启动，基本不存在启动失败的问题。  
![容灾结构](http://codemacro.com/assets/res/diamond/failover.PNG)