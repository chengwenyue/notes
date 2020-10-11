# Netty介绍

功能列表：

- Unified API for various transport types - blocking and non-blocking socket
- Based on a flexible and extensible event model which allows clear separation of concerns
- Highly customizable thread model - single thread, one or more thread pools such as SEDA
- True connectionless datagram socket support (since 3.1)



## 1. EventLoop 体系



### 1.1 EventLoopGroup

EventLoopGroup，它由EventLoop的组成的，采用轮询的方式选择组中的EventLoop去注册Channel，接口继承图如下：

<img src="../../images/EventLoopGroup.png" alt="EventLoopGroup" style="zoom:50%;" />

1. 



## 2. Channel体系



## 3. ChannelHandler体系



 ## 4. ChannelHandlerContext体系



 ## 5. ChannelPipeline 体系



## 6. ChannelFuture体系



 ## 7.  CodeC体系

