docker pull bitnami/testlink:latest
docker pull mysql:5.7.20


docker network create testlink-tier

docker run --name first-mysql --net testlink-tier -e MYSQL\_ROOT\_PASSWORD=123456 -d mysql:5.7.20

docker run -d --name mysql-testlink \
--net testlink-tier \
-v ~/testlink/db:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-e MYSQL_ROOT_HOST=% \
-e MYSQL_DATABASE=testlinkdb \
mysql:5.7.20

docker run -d -p 8000:80 -p 8443:443 --name testlink \
-e MARIADB_USER=root \
-e MARIADB_PASSWORD=123456 \
-e MARIADB_HOST=first-mysql \
-e MARIADB_PORT_NUMBER=3306 \
--net testlink-tier \
--volume ~/testlink/data:/bitnami/testlink \
--volume ~/testlink/apache:/bitnami/apache \
--volume ~/testlink/php:/bitnami/php \
bitnami/testlink:latest

docker run --name jira -d -p 8080:8080 -v /c/Users/31010/Desktop/JIRA:/data --net testlink-tier cptactionhank/atlassian-jira:latest


###列出数据卷和容器的关系

	docker inspect --format '{{.Name}} => {{with .Mounts}}{{range .}}
	    {{.Name}},{{end}}{{end}}' $(docker ps -aq)

###删除无用的数据卷
	docker volume ls   --filter dangling=true | grep local |awk '{print $2}'|xargs docker volume rm


ps -ef | grep /home/test/tomcat | grep -v grep | grep -v catalina.sh |awk '{print $2}' | xargs kill -9
