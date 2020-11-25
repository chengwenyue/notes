### Java Ops

	-Xmx258m 堆最小值。
	-Xms516m 堆最大值。此值可以设置与-Xmx相同，以避免每次垃圾回收完成后JVM重新分配内存。
	-Xmn1m 年轻代大小
	-Xss1024K 设定每个线程的堆栈大小
	
	JAVA_OPTS='-Xms256m -Xmx512m'
	
	-XX:PermSize=256m -XX:MaxPermSize=512m  jdk1.7的永久代大小限制，1.8中无效
	
	-XX:MetaspaceSize=128m  jdk1.8元空间初始大小
	-XX:MaxMetaspaceSize=320m  元空间最大值
	 
	-XX:+HeapDumpOnOutOfMemoryError 当jvm内存溢出时，dump内存
	-XX:+PrintGCTimeStamps 打印gc时间戳
	-XX:+PrintGCDetails 打印gc细节
	-XX:+PrintHeapAtGC 在gc前后打印堆信息
	-Xloggc:gc.log  gc日志文件
	-verbose:gc 打印gc日志


​	
​	-XX:+HeapDumpOnOutOfMemoryError 设置当内存溢出时dump内存
​	-XX:HeapDumpPath=/tmp/heapdump.hprof 设置dump的路径


### java dump线程信息

	jstack -l 8909  > mydumps.tdump


​	
​	jstack -h # 帮助日志
​	
​	Usage:
​	jstack [-l] <pid>
​	    (to connect to running process)
​	jstack -F [-m] [-l] <pid>
​	    (to connect to a hung process)
​	jstack [-m] [-l] <executable> <core>
​	    (to connect to a core file)
​	jstack [-m] [-l] [server_id@]<remote server IP or hostname>
​	    (to connect to a remote debug server)
​	
​	Options:
​	    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
​	    -m  to print both java and native frames (mixed mode)
​	    -l  long listing. Prints additional information about locks
​	    -h or -help to print this help message

### java dump线程分析工具

[IBMThreadDumpAnalyzer](https://www.ibm.com/support/pages/ibm-thread-and-monitor-dump-analyzer-java-tmda)


### java dump内存信息


	jmap -dump:format=b,file=heapdump.phrof pid
	
	加上live只指定活动的对象
	jmap -dump:live,format=b,file=dump.hprof pid



>这个命令执行，JVM会将整个heap的信息dump写入到一个文件，heap如果比较大的话，就会导致这个过程比较耗时，并且执行的过程中为了保证dump的信息是可靠的，所以会暂停应用， 线上系统慎用。


### java 内存分析工具

Memory Analyzer Tool



l查看进程堆栈信息：gstack 进程号> s.log
l查看tomcat下消耗性能最多的线程：top -Hp tomcat进程号
l查看GC情况：jstat -gcutil pid tomcat进程号
l来看看那些对象大量存在回收不掉：jmap -histo pid |more 
l查看线程堵塞：jstack pid
l查看当前进程的线程：top -H -p <pid>
l查看某个进程的连接数：netstat -atp | grep tomcat进程号| wc -l



### Java HSDB工具

windows

```
java -classpath "%JAVA_HOME%/lib/sa-jdi.jar" sun.jvm.hotspot.HSDB
```





###参考博客

[https://www.jianshu.com/p/a4ad53179df3](https://www.jianshu.com/p/a4ad53179df3)
[https://blog.csdn.net/wangxiaotongfan/article/details/82560739](https://blog.csdn.net/wangxiaotongfan/article/details/82560739)