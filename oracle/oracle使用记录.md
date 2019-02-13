##oracle使用记录

### oracle启动

**1.oracle启动监听**

	lsnrctl start #启动监听
	lsnrctl status #查看监听状态
	lsnrctl stop #停止监听

**2.oracle启动实例**

使用sqlplus连接

	sqlplus / as sysdba
启动实例
	
	startup #启动
	shutdown #关闭


### oracle启动ORA-00119、ORA-00130 报错

原因：linux主机名与oracle监听中的主机名不一致

	hostname 查看主机名
	
	oracle监听文件
	/u01/app/oracle/product/11.2.0.4/db_1/network/admin/listener.ora

[Linux系统主机名变成bogon的解决方法](https://blog.csdn.net/ttxsely/article/details/78252685)

	