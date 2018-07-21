### 列出数据卷和容器的关系

	docker inspect --format '{{.Name}} => {{with .Mounts}}{{range .}}
	    {{.Name}},{{end}}{{end}}' $(docker ps -aq)

### 删除无用的数据卷
	docker volume ls   --filter dangling=true | grep local |awk '{print $2}'|xargs docker volume rm

### 数据卷权限问题

查看容器用户

 	docker run -ti --rm --entrypoint="/bin/bash" jenkins -c "whoami && id"

### 查看各个日志的文件大小
	
	ls -lh $(find /var/lib/docker/containers/ -name *-json.log)
