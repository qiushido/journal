# Pinpoint入门  

> Pinpoint是一个分析大型分布式系统的平台，提供解决方案来处理海量跟踪数据。2012年七月开始开发，2015年1月9日作为开源项目启动。  
> Pinpoint(一款APM)跟关注在对于系统内部执行、系统间调用的性能瓶颈分析，利于定位到问题的具体原因  
> 国内类似厂商：oneAPM，听云，云智慧等  
参考链接：  https://skyao.io/learning-pinpoint/introduction/    
>   
##### Pinpoint的特点如下:
- 分布式事务跟踪，跟踪跨分布式应用的消息
- 自动检测应用拓扑，帮助你搞清楚应用的架构
- 水平扩展以便支持大规模服务器集群
- 提供代码级别的可见性以便轻松定位失败点和瓶颈
- 使用字节码增强技术，添加新功能而无需修改代码
 

## Pinpoint技术概览  
> pinpoint跟踪单个的事务分布式请求，基于Goolge Dapper  

### 分布式消息追踪的原理
如下图1,表现的一个消息从Node1到Node2的过程
![分布式系统中的消息关系](https://github.com/naver/pinpoint/raw/master/doc/img/td_figure1.png)
- Google Dapper的分布式事务追踪的核心就是*`识别`*Node1处理的消息和Node2处理的消息之间的关系。Dapper通过例如在Http的header中加入标签，使用识别标签的形式实现消息跟踪。  
- Pinpoint基于google dapper的跟踪技术,但是已经修改为在`调用的header`中添加应用级别标签数据以便在远程调用中跟踪分布式事务。标签数据由多个key组成，定义为TraceId。  

### Pinpoint中的数据结构  
- Pinpoint中，核心数据结构由Span, Trace, 和 TraceId组成。
	- Span 是对RPC发起追踪的基本单位
	- Trace 是多个Span的集合  
	Trace过程共享一个TransactionId
	- TraceId 是单次事务发起的多次RPC的调用的动作集合  
 
		- SpanId  
		与PspanId形成继承树结构
		- ParentSpanId
		- TransactionId（TxId消息ID）全局唯一，单事务发起和接收消息的ID，跨整服务器集群  
		
<h3>TraceId组成</h3>   <center>![TraceId行为示例](https://github.com/naver/pinpoint/raw/master/doc/img/td_figure2.png)</center>   
<h3>Span组成</h3>  <center>![span标签](http://bigbully.github.io/Dapper-translation/images/img3.png)</center>  

<h3>trace时间组成</h3>  <center>![时间组成](http://bigbully.github.io/Dapper-translation/images/img2.png)</center>

### PinpointAgent使用技术  
#### 字节码增强  
> 字节码增强是个很危险的操作。Agent在目标方法添加拦截器的before()和After()方法，在方法中获取部分性能数据  
使用字节码增强的缺点是当Pinpoint自身类库的采样代码出现问题时可能影响应用。不过，可以通过启用或者禁用pinpoint来解决问题，很简单，因为不需要修改代码。

通过增加下面三行到JVM启动脚本中就可以轻易的为应用启用pinpoint：
``` java  
-javaagent:$AGENT_PATH/pinpoint-bootstrap-$VERSION.jar
-Dpinpoint.agentId=<Agent's UniqueId>
-Dpinpoint.applicationName=<The name indicating a same service (AgentId collection)>
```  
如果因为pinpoint发生问题，只需要在JVM启动脚本中删除这些配置数据  
  
## Pinpoint应用示例  
见下图，一次RPC和pinpoint的事务追踪
![trace与pinpoint前端显示](https://github.com/naver/pinpoint/raw/master/doc/img/td_figure5.png)   
下面阐述pinpoint为每个方法做了什么：
```  
当请求到达TomcatA时, Pinpoint agent 产生一个 TraceId.
TX_ID: TomcatA\^TIME\^1
SpanId: 10
ParentSpanId: -1(Root)
从spring MVC 控制器中记录数据

插入HttpClient.execute()方法的调用并在HTTPGet中配置TraceId

创建一个子TraceId

TX_ID: TomcatA\^TIME\^1 -> TomcatA\^TIME\^1
SPAN_ID: 10 -> 20
PARENT_SPAN_ID: -1 -> 10 (父 SpanId)
在HTTP header中配置子 TraceId

HttpGet.setHeader(PINPOINT_TX_ID, "TomcatA\^TIME\^1")
HttpGet.setHeader(PINPOINT_SPAN_ID, "20")
HttpGet.setHeader(PINPOINT_PARENT_SPAN_ID, "10")
传输打好tag的请求到TomcatB.

TomcatB 检查传输过来的请求的header

HttpServletRequest.getHeader(PINPOINT_TX_ID)

TomcatB 作为子节点工作因为它识别了header中的TraceId

TX_ID: TomcatA\^TIME\^1
SPAN_ID: 20
PARENT_SPAN_ID: 10
从spring mvc控制器中记录数据并完成请求
```  
![应用](https://github.com/naver/pinpoint/raw/master/doc/img/td_figure6.png)
图6 示例2：pinpoint应用
```  
当从tomcatB回来的请求完成时，pinpoint agent发送跟踪数据到pinpoint collector就此存储在HBase中
在对tomcatB的HTTP调用结束后，TomcatA的请求也完成了。pinpoint agent发送跟踪数据到pinpoint collector就此存储在HBase中
UI从HBase中读取跟踪数据并通过排序树来创建调用栈
```  
