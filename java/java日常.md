### 名词
`assembly`：简单的说，maven-assembly-plugin 就是用来帮助打包用的，比如说打出一个什么类型的包，包里包括哪些内容等等。

`strom`:Storm 大数据实事计算系统，是Twitter开源的一个分布式的实时计算系统

`paxos`:Paxos算法是基于消息传递且具有高度容错特性的一致性算法，是目前公认的解决分布式一致性问题最有效的算法之一。

`Disruptor`：Disruptor它是一个开源的并发框架，并获得2011 Duke’s 程序框架创新奖，能够在无锁的情况下实现网络的Queue并发操作。

`ELK` ：ELK是三个开源软件的缩写，分别表示：Elasticsearch , Logstash, Kibana , 它们都是开源软件。新增了一个FileBeat，它是一个轻量级的日志收集处理工具(Agent)，Filebeat占用资源少，适合于在各个服务器上搜集日志后传输给Logstash，官方也推荐此工具。

`FIFO/LRU/LFU `:三种缓存算法。

### java 组件

`Excel4J`：读取excel的组件

### Java正则匹配卡死现象

>
正则：[^&#=]+=(([^>&=]+)*([\[]*<![^>&=]+>[\]]*)+([^>&=]+)*)+

>匹配模版Demo：关键词=<!经常近类>+<!外面近类>+<!没有近类>+<!办理近类>+<!宽带近类>

>卡死字符串：针对问题="我这辈子做的最烂的决定就是申购了这个基金，我怎么把它送回老家拿回买它的钱？"

### linux 后台运行java

	nohup java -jar xxxx.jar > myout.out 2>&1 &
	

nohup 命令使用

	nohup command > myout.file 2>&1 &
	在上面的例子中，0 – stdin (standard input)，1 – stdout (standard output)，2 – stderr (standard error) ；
	2>&1是将标准错误（2）重定向到标准输出（&1），标准输出（&1）再被重定向输入到myout.file文件中。



