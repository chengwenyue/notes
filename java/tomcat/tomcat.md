# Tomcat 8.5



## 1. tomcat 类结构

Server.xml中描述的也是Tomcat 中类的层次结构。

Server中包含Service

Service包含

1. Connector 多个
2. Engine 唯一
3. Mapper，映射器，将Request请求映射到具体的Host,Context,Wrapper。

Connector包含协议Protocol，默认使用Http11NioProtocol

Protocol中有

1. Endpoint（默认为NioEndpoint）用于接收socket连接。
2. ConnectionHandler 用于处理socket的数据。
3. Adapter 默认为CoyoteAdapter，用与处理http请求。

NioEndpoint有三大组件

1. Acceptor，轮询socket连接请求
2. Poller，轮询socket读写请求
3. SocketProcessor，在线程池中异步处理socket数据

ConnectionHandler中调用Protocol的createProcessor生成协议处理器，并维护Processor（默认为Http11Processor）。

Http11Processor创建Request，Response，并读取Socket数据填充至Request，调用Adapter处理

1. Adapter 
2. Request
3. Response

CoyoteAdapter解析Request，执行**请求映射** ，并调用Engine的管道处理请求。

1. connector

Engine



