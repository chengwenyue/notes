## oracle linux安装

### 1.安装版本
	
oracle版本：`oracle 11g 11.2.0.4`

linux系统：`centos7`

oracle7个安装包介绍：[oracle7个安装包介绍](https://www.cnblogs.com/Jingkunliu/archive/2018/11/19/9984069.html)

### 2.依赖包安装

	#The following or later version of packages for Oracle Linux 6, Red Hat Enterprise Linux 6, and Asianux Server 4 must be installed:

	binutils-2.20.51.0.2-5.11.el6 (x86_64)
	compat-libcap1-1.10-1 (x86_64)
	compat-libstdc++-33-3.2.3-69.el6 (x86_64)
	compat-libstdc++-33-3.2.3-69.el6.i686
	glibc-2.12-1.7.el6 (i686)
	glibc-2.12-1.7.el6 (x86_64)
	glibc-devel-2.12-1.7.el6 (x86_64)
	glibc-devel-2.12-1.7.el6.i686
	gcc-4.4.4-13.el6 (x86_64)
	gcc-c++-4.4.4-13.el6 (x86_64)
	ksh
	libgcc-4.4.4-13.el6 (i686)
	libgcc-4.4.4-13.el6 (x86_64)
	libstdc++-4.4.4-13.el6 (x86_64)
	libstdc++-4.4.4-13.el6.i686
	libstdc++-devel-4.4.4-13.el6 (x86_64)
	libstdc++-devel-4.4.4-13.el6.i686
	libaio-0.3.107-10.el6 (x86_64)
	libaio-0.3.107-10.el6.i686
	libaio-devel-0.3.107-10.el6 (x86_64)
	libaio-devel-0.3.107-10.el6.i686
	make-3.81-19.el6
	sysstat-9.0.4-11.el6 (x86_64)


	# The following or later version of packages for Oracle Linux 4 and Red Hat Enterprise Linux 4 must be installed:

	binutils-2.15.92.0.2
	compat-libstdc++-33-3.2.3
	compat-libstdc++-33-3.2.3 (32 bit)
	elfutils-libelf-0.97
	elfutils-libelf-devel-0.97
	expat-1.95.7
	gcc-3.4.6
	gcc-c++-3.4.6
	glibc-2.3.4-2.41
	glibc-2.3.4-2.41 (32 bit)
	glibc-common-2.3.4
	glibc-devel-2.3.4
	glibc-headers-2.3.4
	libaio-0.3.105
	libaio-0.3.105 (32 bit)
	libaio-devel-0.3.105
	libaio-devel-0.3.105 (32 bit)
	libgcc-3.4.6
	libgcc-3.4.6 (32-bit)
	libstdc++-3.4.6
	libstdc++-3.4.6 (32 bit)
	libstdc++-devel 3.4.6
	make-3.80
	numactl-0.6.4.x86_64
	pdksh-5.2.14
	sysstat-5.0.5
	
	# 执行安装命令
	yum -y install binutils compat-libcap1 compat-libstdc++-33 glibc \
	glibc-devel gcc gcc-c++ elfutils-libelf-devel elfutils-libelf \
	expat ksh \
	libgcc libstdc++ libstdc++-devel libaio libaio-devel make numactl \ 
	glibc glibc-common glibc-devel glibc-headers \
	sysstat pdksh libXp

### 3.创建oracle用户,组

	# groupadd oinstall
	# groupadd dba
	# useradd -g oinstall -G dba oracle（主组oinstall，其它组：dba）
	# passwd oracle

### 4.修改操作系统参数

配置内核参数：

可以直接在`/etc/sysctl.conf`文件中添加以下参数：

	fs.aio-max-nr = 1048576
	fs.file-max = 6815744
	kernel.shmall = 2097152
	kernel.shmmax = 4294967295
	kernel.shmmni = 4096
	kernel.sem = 250 32000 100 128
	net.ipv4.ip_local_port_range = 9000 65500
	net.core.rmem_default = 262144
	net.core.rmem_max = 4194304
	net.core.wmem_default = 262144
	net.core.wmem_max = 1048576

然后使用`/sbin/sysctl -p`   --使内核参数实时生效

### 5.修改用户的限制文件

编辑 `/etc/security/limits.conf`
添加以下配置：

	oracle              soft    nproc  2047
	oracle              hard    nproc  16384
	oracle              soft    nofile 1024
	oracle              hard    nofile 65536
	oracle              soft    stack  10240

### 6.建立oracle安装目录

**建立安装目录**

创建oracle用户目录: `mkdir -p /u01/app/oracle`

**指定所有者、组和权限**

	chown -R oracle:oinstall /u01/app/
	
	chmod -R 775 /u01/app/

**在/etc/profile末尾增加oracle相关限制**


	#Oracle判断
	if [ $USER = "oracle" ]; then
	if [ $SHELL = "/bin/ksh" ]; then
	ulimit -p 16384
	ulimit -n 65536
	else
	ulimit -u 16384 -n 65536
	fi
	umask 022 
	fi
### 7.设置Oracle用户环境变量

编辑Oracle环境变量配置文件:`/home/oracle/.bash_profile`

添加以下内容：

	export ORACLE_BASE=/u01/app
	export ORACLE_HOME=$ORACLE_BASE/oracle/product/11.2.0.4/db_1
	export PATH=$ORACLE_HOME/bin:$PATH
	export ORACLE_SID=orcl  #默认实例名
	export CLASSPATH=$ORACLE_HOME/JRE:$ORACLE_HOME/jlib:$ORACLE_HOME/rdbms/jlib
	#export NLS_LANG=AMERICAN_AMERICA.ZHS16GBK/UTF8  #数据库编码，ZHS16GBK/UTF8任选一个
	export NLS_DATE_FORMAT=YYYY/MM/DD #数据库时间格式
	#以下两个设置是用来设置rlwrap插件的环境，用来支持命令行回显功能
	alias sqlplus="rlwrap sqlplus"
	alias rman="rlwrap rman"

source /home/oracle/.bash_profile  --使配置生效

### 8. 安装数据库软件（oracle用户）

准备Oracle11g安装文件并解压

	unzip oracle压缩文件1.zip -d/u01/app/
	unzip oracle压缩文件2.zip -d/u01/app/

**注意：oracle有7个文件，安装oracle，只需要前两个文件**

### 9. 添加主机名称
切换到root用户下

执行hostname查看主机名称

vim /etc/hosts 查看是否有hostname显示的主机名称

如果没有就添加上

### 10. 静默安装


	cd /u01/app/database/response
	
	cp db_install.rsp db_install.rsp.bak
	
	vi db_install.rsp

添加以下内容：

查看配置项：`grep -Ev "^$|^#" db_install.rsp` 

	oracle.install.responseFileVersion=/oracle/install/rspfmt_dbinstall_response_schema_v11_2_0
	oracle.install.option=INSTALL_DB_SWONLY
	ORACLE_HOSTNAME=
	UNIX_GROUP_NAME=oinstall
	INVENTORY_LOCATION=/u01/app/oracle/oraInventory
	SELECTED_LANGUAGES=en,zh_CN
	ORACLE_HOME=/u01/app/oracle/product/11.2.0.4/db_1
	ORACLE_BASE=/u01/app
	oracle.install.db.InstallEdition=EE
	oracle.install.db.EEOptionsSelection=false
	oracle.install.db.optionalComponents=oracle.rdbms.partitioning:11.2.0.4.0,oracle.oraolap:11.2.0.4.0,oracle.rdbms.dm:11.2.0.4.0,oracle.rdbms.dv:11.2.0.4.0,oracle.rdbms.lbac:11.2.0.4.0,oracle.rdbms.rat:11.2.0.4.0
	oracle.install.db.DBA_GROUP=dba
	oracle.install.db.OPER_GROUP=oinstall
	oracle.install.db.CLUSTER_NODES=
	oracle.install.db.isRACOneInstall=
	oracle.install.db.racOneServiceName=
	oracle.install.db.config.starterdb.type=GENERAL_PURPOSE
	oracle.install.db.config.starterdb.globalDBName=orcl
	oracle.install.db.config.starterdb.SID=orcl
	oracle.install.db.config.starterdb.characterSet=AL32UTF8
	oracle.install.db.config.starterdb.memoryOption=true
	oracle.install.db.config.starterdb.memoryLimit=512
	oracle.install.db.config.starterdb.installExampleSchemas=false
	oracle.install.db.config.starterdb.enableSecuritySettings=true
	oracle.install.db.config.starterdb.password.ALL=oracle
	oracle.install.db.config.starterdb.password.SYS=
	oracle.install.db.config.starterdb.password.SYSTEM=
	oracle.install.db.config.starterdb.password.SYSMAN=
	oracle.install.db.config.starterdb.password.DBSNMP=
	oracle.install.db.config.starterdb.control=DB_CONTROL
	oracle.install.db.config.starterdb.gridcontrol.gridControlServiceURL=
	oracle.install.db.config.starterdb.automatedBackup.enable=false
	oracle.install.db.config.starterdb.automatedBackup.osuid=
	oracle.install.db.config.starterdb.automatedBackup.ospwd=
	oracle.install.db.config.starterdb.storageType=FILE_SYSTEM_STORAGE
	oracle.install.db.config.starterdb.fileSystemStorage.dataLocation=/u01/app/oracle/oradata
	oracle.install.db.config.starterdb.fileSystemStorage.recoveryLocation=/u01/app/oracle/fast_recovery_area
	oracle.install.db.config.asm.diskGroup=
	oracle.install.db.config.asm.ASMSNMPPassword=
	MYORACLESUPPORT_USERNAME=
	MYORACLESUPPORT_PASSWORD=
	SECURITY_UPDATES_VIA_MYORACLESUPPORT=
	DECLINE_SECURITY_UPDATES=true
	PROXY_HOST=
	PROXY_PORT=
	PROXY_USER=
	PROXY_PWD=
	PROXY_REALM=
	COLLECTOR_SUPPORTHUB_URL=
	oracle.installer.autoupdates.option=SKIP_UPDATES
	oracle.installer.autoupdates.downloadUpdatesLoc=
	AUTOUPDATES_MYORACLESUPPORT_USERNAME=
	AUTOUPDATES_MYORACLESUPPORT_PASSWORD=


开始安装：

	cd /u01/app/database

	./runInstaller -silent -ignoreSysPrereqs -showProgress -responseFile /u01/app/database/response/db_install.rsp

	其中-silent指的是静默安装，-ignorePrereq忽略prerequisite的检查结果，showProgress显示进度，responseFile是种子文件。

### 11.用oracle用户登录配置监听

netca.rsp不需要修改,直接建立监听

	netca -silent -responseFile /u01/app/database/response/netca.rsp

### 12.建立新库，同时建立对应的实例

编辑 dbca.rsp
修改如下内容

	GDBNAME = "orcl"
	SID = "orcl"
	TEMPLATENAME = "General_Purpose.dbc"
	SYSPASSWORD = "oracle"
	SYSTEMPASSWORD = "oracle"
	SYSMANPASSWORD = "oracle"
	DBSNMPPASSWORD = "oracle"
	DATAFILEDESTINATION =/u01/app/oracle/oradata
	RECOVERYAREADESTINATION=/u01/app/oracle/fast_recovery_area
	CHARACTERSET = "ZHS16GBK"
	TOTALMEMORY = "1638"

执行命令

	dbca -silent -responseFile /u01/app/database/response/dbca.rsp

### 安装sqlplus rlwrap

sqlplus安装包

	rpm -ivh oracle-instantclient11.2-basic-11.2.0.4.0-1.x86_64.rpm
	rpm -ivh oracle-instantclient11.2-sqlplus-11.2.0.4.0-1.x86_64.rpm

rlwrap 依赖包
	yum -y install autoconf


rlwrap安装包

	rpm -ivh rlwrap-0.42-1.el6.x86_64.rpm

### oralce自启动

	vim /etc/rc.d/rc.local

	su oracle -lc /home/oracle/app/oracle/product/11.2.0.4/db_1/bin/dbstart

https://blog.csdn.net/weixin_43301601/article/details/87883000
