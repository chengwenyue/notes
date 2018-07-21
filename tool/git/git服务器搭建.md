### git服务器搭建

可以在本地搭建类似github的服务，用于git的托管。

- gitlab

>gitlab的功能十分完善，但是运行时比较占用资源，如果只是个人开发不建议使用。


- gitblit

>gitblit可以看作gitlab的缩减版，只包含核心功能，能够满足个人日常学习使用。

- gogs

> 功能和界面都比较完善，而且安装配置简单，且不占用资源，本文就使用该应用。

注：详细资料可以去百度。


#### 1.准备

先安装git

	apt install git

在gogs官网上下载安装包，下载地址 [gogs_0.11.53_linux_amd64.tar.gz](https://dl.gogs.io/0.11.53/gogs_0.11.53_linux_amd64.tar.gz)

- gogs_0.11.53_linux_amd64.tar.gz

在`/usr/local`目录下新建目录`gogs`

	mkdir -p /usr/local/gogs

将安装包解压到gogs目录

	tar -zxvf gogs_0.11.53_linux_amd64.tar.gz -C /usr/local/gogs/
#### 2.启动gogs应用

在gogs目录下使用命令启动gogs

	./gogs web

在浏览器上输入http://ip:3000即可访问

#### 3.设置开机自启

先将`gogs/scripts/init/debian/gogs`文件拷贝到`/etc/init.d/`目录下:

	cp gogs/scripts/init/debian/gogs /etc/init.d/
增加运行权限

	sudo chmod +x /etc/init.d/gogs

修改此文件

	WORKINGDIR=/usr/local/gogs/gogs #这个根据自己的目录修改
	USER=root #如果运行gogs不是用的这个用户，修改对应用户

用下面命令来启动关闭gogs服务

	/etc/init.d/gogs start|restart|stop|status

添加到开机自启

	systemctl enable gogs

