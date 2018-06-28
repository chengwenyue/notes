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