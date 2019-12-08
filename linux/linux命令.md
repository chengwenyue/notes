## linux日常记录
### find

查看空文件夹

	find -type d -empty

### systemctl


重新加载service文件

	systemctl daemon-reload

启动一个服务

	systemctl start nginx-1.13.0.service

设置开机自启动

	systemctl enable nfs-server.service

停止开机自启动

	systemctl disable nfs-server.service

查看服务当前状态

	systemctl status nfs-server.service


### 服务器带宽测试

#### iperf
	
**服务端**
	
	iperf -s

**客户端**

	iperf -c <server ip>

**iperf常用选项**

	##通用选项
	-f <kmKM>    报告输出格式。 [kmKM]   format to report: Kbits, Mbits, KBytes, MBytes
	-i <sec>    在周期性报告带宽之间暂停n秒。如周期是10s，则-i指定为2，则每隔2秒报告一次带宽测试情况,则共计报告5次
	-p    设置服务端监听的端口，默认是5001
	-u    使用UDP协议测试
	-w n<K/M>   指定TCP窗口大小
	-m    输出MTU大小
	-M    设置MTU大小
	-o <filename>    结果输出至文件
	
	##服务端选项
	-s    iperf服务器模式
	-d    以后台模式运行服务端
	-U    运行一个单一线程的UDP模式

	##客户端选项
	
	-b , --bandwidth n[KM]    指定客户端通过UDP协议发送数据的带宽（bit/s）。默认是1Mbit/s
	-c <ServerIP>    以客户端模式运行iperf，并且连接至服务端主机ServerIP。 eg:  iperf -c <server_ip>
	-d    双向测试
	-t    指定iperf带宽测试时间，默认是10s。  eg:  iperf -c <server_ip> -t 20
	-P    指定客户端并发线程数，默认只运行一个线程。 eg,指定3个线程 : iperf -c <server_ip> -P 3
	-T    指定TTL值

例子：

	服务端：
	perf -s -i 2
	客户端：
	iperf -c 192.168.159.135 -i 2

以上测试都是客户端到服务端的上行带宽测试，还需要进行下行测试。

#### 查看系统位数

	getconf LONG_BIT

#### 移除过期的SSH密钥

	ssh-keygen -f "/root/.ssh/known_hosts" -R 192.168.0.106

#### 删除虚拟网卡

	ifconfig <网桥名> down

#### 查看cpu信息

	cat /proc/cpuinfo 
	
	cat /proc/cpuinfo | grep processor | wc -l

#### 查看安装过的包

	dpkg -l

### zip压缩

	将目录html压缩到当前目录的html.zip中
	zip -q -r html.zip /home/Blinux/html

### tar压缩

	tar -cvf log.tar log2012.log    仅打包，不压缩！ 
	tar -zcvf log.tar.gz log2012.log   打包后，以 gzip 压缩 
	tar -jcvf log.tar.bz2 log2012.log  打包后，以 bzip2 压缩 

	--no-same-owner 目录权限会使用当前操作用户的权限作为文件的所属
查阅tar包内有哪些文件：

	tar -ztvf log.tar.gz

一次性解压多个文件

	for tar in *.tar.gz; do tar xvf $tar; done


tar打包排除某个目录
 	tar zcvf fd.tar.gz * --exclude=file1 --exclude=dir1

注意：

1、--exclude=file1 而不是 --exclude file1

2、要排除一个目录是--exclude=dir1而不是--exclude=dir1/

也可以在父目录打包

	tar zcvf fd.tar.gz pardir --exclude=pardir/file1 --exclude=pardir/dir1


### centos7 修改时间和时区

	timedatectl set-local-rtc 1 # 将硬件时钟调整为与本地时钟一致, 0 为设置为 UTC 时间
	或者 hwclock --systohc --localtime 

	timedatectl set-timezone Asia/Shanghai # 设置系统时区为上海
	
	替换/etc/localtime文件
	[root@test ~]# mv /etc/localtime /etc/localtime.bak
	[root@test ~]# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

### 记一次linux隐藏进程号的方法

1.正常启动进程

例如启动redis服务

	bin/redis-server conf/redis.conf

启动后可用ps查看进程
	
	ps -ef | grep redis
输出：
	
	root       1237      1  0 14:01 ?        00:00:00 bin/redis-server 127.0.0.1:6379

2.新建一个文件夹

	mkdir myshell

文件夹的索引号为：`933634`

	933634 drwxr-xr-x 2 root root  4096 Sep  5 14:03 myshell

3.使用mount将`/proc/<redis-pid>`挂载到`myshell`目录

	mount -B myshell /proc/1237/
这时使用`ps -ef`查看不到redis服务进程，但是redis服务正常运行。

原理：利用mount -B 命令，将`/proc`下进程信息隐藏。此时`/proc/1237`文件夹的索引号`933634`与`myshell`文件夹相同。

	ls -lai /proc/ | grep 1237
	933634 drwxr-xr-x   2 root             root                        4096 Sep  5 14:03 1237

### scp使用

写法
>scp [可选参数] file_source file_target 

参数说明：

	-r： 递归复制整个目录。
命令格式：


	scp local_file remote_username@remote_ip:remote_folder 
	或者 
	scp local_file remote_username@remote_ip:remote_file 
	或者 
	scp local_file remote_ip:remote_folder 
	或者 
	scp local_file remote_ip:remote_file 


### 添加用户

添加用户命令：

	adduser tmp_3452
修改密码命令：

	passwd tmp_3452
删除用户

	
	userdel tmp_3452
	-f 连同用户文件夹一起删除

### linux文本处理

[linux文本处理.md](./linux文本处理.md)

### ssh 本地端口转发

[SSH 端口转发](https://www.ibm.com/developerworks/cn/linux/l-cn-sshforward/)

	ssh -L 7001:172.16.0.105:1521 -N root@180.153.71.113
	
	ssh -L 7001:172.16.0.105:1521 -N root@180.153.71.113

### nohup 命令使用

	nohup command > myout.file 2>&1 &

	在上面的例子中，0 – stdin (standard input)，1 – stdout (standard output)，2 – stderr (standard error) ；
	2>&1是将标准错误（2）重定向到标准输出（&1），标准输出（&1）再被重定向输入到myout.file文件中。


### sed命令

查看5-10行的内容

	sed -n '5,10p' filename

### iconv 编码转换

-f from 原编码
-t to 转换后编码

将文件file 编码从UFT-8转换为GB2312:

	iconv file -f UTF-8 -t GB2312 -o file