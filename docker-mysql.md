###mysql参数

`MYSQL_HOST`：设置mysql主机名<br>
`MYSQL_ROOT_PASSWORD`：设置root密码<br>
`MYSQL_ROOT_HOST`：设置允许访问的主机


	docker run --name first-mysql \
	--net testlink-tier \
	-e MYSQL_ROOT_PASSWORD=123456 \
	-d mysql:5.7.20

-net testlink-tier:选择网络（docker network create）

###mysql数据存储在本地

映射本地目录到`/var/lib/mysql`
	-v 本地数据目录:/var/lib/mysql \

###mysql指定配置文件

	-v 本地文件地址:/etc/mysql/mysql.conf.d/mysqld.cnf


>例如
>
	docker run -d -p 3306:3306 \
	-v /soft/mysql/my.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf \
	-v /soft/mysql/data:/var/lib/mysql \
	-e MYSQL_ROOT_PASSWORD=123456 \
	--name mymysql mysql:5.7.18
	

	docker run -d --name mysql-testlink \
	--net testlink-tier \
	-v ~/testlink/db:/var/lib/mysql \
	-e MYSQL_ROOT_PASSWORD=123456 \
	-e MYSQL_ROOT_HOST=% \
	-e MYSQL_DATABASE=testlinkdb \
	mysql:5.7.20


###docker mysql设置mysql编码为utf8

	--character-set-server=utf8mb4 \
	--collation-server=utf8mb4_unicode_ci 

###mysql配置文件
路径：`/etc/mysql/mysql.conf.d/mysqld.cnf`<br>
内容：

	[mysqld]
	pid-file    = /var/run/mysqld/mysqld.pid
	socket      = /var/run/mysqld/mysqld.sock
	datadir     = /var/lib/mysql
	#log-error  = /var/log/mysql/error.log
	# By default we only accept connections from localhost
	#bind-address   = 127.0.0.1
	# Disabling symbolic-links is recommended to prevent assorted security risks
	#symbolic-links=0

### 
	docker run --name mysql2 \
	--net testlink-tier \
	-p 3307:3306 \
	-v /etc/mysql/my.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf \
	-e MYSQL_ROOT_PASSWORD=123456 \
	-e MYSQL_HOST=mysql2 \
	-e MYSQL_ROOT_HOST=% \
	-d mysql:5.7.20 \
	--character-set-server=utf8mb4 \
	--collation-server=utf8mb4_unicode_ci 


###mysql查看编码

	SHOW VARIABLES LIKE 'character_set_%';