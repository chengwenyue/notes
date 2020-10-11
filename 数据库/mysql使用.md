## mysql使用

### mysql授权数据库

	grant all privileges on db to test@'%' identified by 'passwd';
	flush privileges;

