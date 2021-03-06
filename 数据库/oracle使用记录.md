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

​	

### oracle数据库死锁解决

查看死锁的sql语句

	select sql_text from v$sql where hash_value in 
	(select sql_hash_value from v$session where sid in
	(select session_id from v$locked_object));


单表解锁

1.执行如下sql，然后把结果执行一边

	select 'alter system kill session ' || '''' ||s.sid || ',' || s.serial# || '''' ||'; '
	from v$locked_object l,dba_objects o ,v$session s
	where l.object_id　=　o.object_id and l.session_id=s.sid and object_name ='WORDPAT';

2、下面的语句用来杀死一个进程：

	alter system kill session '24,111'; 
	(其中24,111分别是上面查询出的sid,serial#)

参考 [https://blog.csdn.net/solid_j/article/details/80026829](https://blog.csdn.net/solid_j/article/details/80026829)


### oralce 从一个用户expdp导出再impdp导入到另一个用户

如果想导入的用户已经存在:

1. 导出用户 expdp user1/pass1 directory=dumpdir dumpfile=user1.dmp
2. 导入用户 impdp user2/pass2 directory=dumpdir dumpfile=user1.dmp REMAP_SCHEMA=user1:user2 EXCLUDE=USER

如果想导入的用户不存在:

1. 导出用户 expdp user1/pass1 directory=dumpdir dumpfile=user1.dmp
2. 导入用户 impdp system/passsystem directory=dumpdir dumpfile=user1.dmp REMAP_SCHEMA=user1:user2
3. user2会自动建立，其权限和使用的表空间与user1相同，但此时用user2无法登录，必须修改user2的密码


### oracle导入dmp文件


按用户导入

	impdp kms/nkipwdu directory=qldb dumpfile=znkf_zo_2019_06_25_12.dmp remap_tablespace=kms_cloud:kms remap_schema=znkf_zo:kms logfile=20190627.log;

按表导入

	impdp B/passwdtables=A.table1,A.table2 remap_schema=A:B directory=data_dir dumpfile=expdp.dmp logfile=impdp.log;




参考[https://www.cnblogs.com/promise-x/p/7477360.html](https://www.cnblogs.com/promise-x/p/7477360.html)
