## ubuntu 二进制安装mysql

### 准备

1.资源包

- [mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz](https://dev.mysql.com/downloads/mysql/5.6.html#downloads)


2.安装mysql的依赖库

	shell> apt-cache search libaio # search for info
	shell> apt-get install libaio1 # install library
	shell> apt-get install -y perl pwgen --no-install-recommends

### 安装mysql

1.添加mysql用户和用户组
	
	groupadd mysql
	useradd -r -g mysql -s /bin/false mysql
2.解压mysql安装包

	mkdir /usr/local/mysql
	tar -zxvf mysql-5.6.40-linux-glibc2.12-x86_64.tar.gz -C /usr/local/mysql

3.修改mysql根目录的用户

	chown -R mysql:mysql /usr/local/mysql/

4.初始化mysql数据库

[mysql_install_db](https://dev.mysql.com/doc/refman/5.6/en/mysql-install-db.html)初始化MySQL数据目录并创建它包含的系统表（如果它们不存在）。

	scripts/mysql_install_db --user=mysql \
	         --basedir=/usr/local/mysql \
	         --datadir=/usr/local/mysql/data

### 启动mysqld

启动mysqld可以使用[mysqld_safe](https://dev.mysql.com/doc/refman/5.6/en/mysqld-safe.html)和[mysql.server](https://dev.mysql.com/doc/refman/5.6/en/mysql-server.html)，mysql.server会使用mysqld_safe命令，
mysqld_safe负责启动mysqld进程。

1.使用mysqld_safe启动mysql

在启动命令中指定数据目录和mysql安装目录

	bin/mysqld_safe --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data/mysql & 
	
	kill <mysqld pid> ## 关闭mysqld


mysqld_safe常用的启动选项，具体参考[mysqld_safe-options](https://dev.mysql.com/doc/refman/5.6/en/mysqld-safe.html)。

		--basedir ## mysql安装位置 
		--datadir ## mysql数据位置（使用mysql_install_db初始化目录）
		--port    ## 监听端口（3306）
		--user    ## 用户（mysql）
		--pid-file ## mysql服务器进程标识文件

注：如果使用mysql配置文件，只需要在配置文件配置参数，mysqld_safe命令就不要指定启动选项就能直接启动。命令如下：

	bin/mysqld_safe &

配置文件内容如下：
	
	# For advice on how to change settings please see
	# http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html
	
	[mysqld]
	
	basedir = /usr/local/mysql/
	datadir = /usr/local/mysql/data/mysql
	port = 3306
	server_id = 1
	#socket = /usr/local/mysql/data/mysql.sock
	user = mysql
	pid-file    = /usr/local/mysql/data/mysql/mysqld.pid
	bind-address   = 0.0.0.0
	sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

2.使用mysql.server启动mysql

编辑mysql.server脚本，增加如下内容：

	vim support-files/mysql.server
	
	basedir=[mysql安装目录]
	datadir=[mysql数据目录]

保存退出后启动mysql

	support-files/mysql.server start

停止mysql

	support-files/mysql.server stop

注：mysql.server命令同样可以使用mysql配置文件。

3.mysql配置文件[my.cnf](https://dev.mysql.com/doc/refman/5.6/en/option-files.html)

linux系统上读取配置文件

<table summary="Option files read by MySQL programs on Unix and Unix-like systems."><colgroup><col width="30%"><col width="70%"></colgroup><thead><tr>
          <th scope="col">File Name</th>
          <th scope="col">Purpose</th>
        </tr></thead><tbody><tr>
          <td scope="row"><code class="filename">/etc/my.cnf</code></td>
          <td>Global options</td>
        </tr><tr>
          <td scope="row"><code class="filename">/etc/mysql/my.cnf</code></td>
          <td>Global options</td>
        </tr><tr>
          <td scope="row"><code class="filename"><em class="replaceable"><code>SYSCONFDIR</code></em>/my.cnf</code></td>
          <td>Global options</td>
        </tr><tr>
          <td scope="row"><code class="filename">$MYSQL_HOME/my.cnf</code></td>
          <td>Server-specific options (server only)</td>
        </tr><tr>
          <td scope="row"><code class="literal">defaults-extra-file</code></td>
          <td>The file specified with
            <code class="option">--defaults-extra-file</code>, if
            any</td>
        </tr><tr>
          <td scope="row"><code class="filename">~/.my.cnf</code></td>
          <td>User-specific options</td>
        </tr><tr>
          <td scope="row"><code class="filename">~/.mylogin.cnf</code></td>
          <td>User-specific login path options (clients only)</td>
</tr></tbody></table>

具体配置项还需参考[官网配置](https://dev.mysql.com/doc/refman/5.6/en/option-files.html)

### mysql开机自启

1.拷贝my.cnf到/etc目录下

	cp my.cnf /etc/my.cnf

2.拷贝mysql.server命令到/etc/init.d/目录下

	cp support-files/mysql.server /etc/init.d/mysqld

3.设置开机自启

	systemctl enable mysqld