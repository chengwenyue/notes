docker pull bitnami/testlink:latest
docker pull mysql:5.7.20


docker network create testlink-tier


###testlink docker 启动
	docker run -d -p 8000:80 -p 8443:443 --name testlink \
	-e MARIADB_USER=root \
	-e MARIADB_PASSWORD=123456 \
	-e MARIADB_HOST=test-mysql \
	-e MARIADB_PORT_NUMBER=3306 \
	--net testlink-tier \
	--volume ~/testlink/data:/bitnami/testlink \
	--volume ~/testlink/apache:/bitnami/apache \
	--volume ~/testlink/php:/bitnami/php \
	bitnami/testlink:latest

###

	docker run --name jira -d -p 8080:8080 -v /c/Users/31010/Desktop/JIRA:/data --net testlink-tier cptactionhank/atlassian-jira:latest

###
	ps -ef | grep /home/test/tomcat | grep -v grep | grep -v catalina.sh |awk '{print $2}' | xargs kill -9


###使用Docker Machine时，如何用root身份操作Docker VM主机

docker-machine ssh 登陆后使用sudo -i命令
