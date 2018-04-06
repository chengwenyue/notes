### 列出数据卷和容器的关系

	docker inspect --format '{{.Name}} => {{with .Mounts}}{{range .}}
	    {{.Name}},{{end}}{{end}}' $(docker ps -aq)

### 删除无用的数据卷
	docker volume ls   --filter dangling=true | grep local |awk '{print $2}'|xargs docker volume rm