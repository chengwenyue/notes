## openstf docker配置

### docker toolbox镜像加速
sudo sed -i "s|EXTRA_ARGS='|EXTRA_ARGS='--registry-mirror=https://registry.docker-cn.com |g" /var/lib/boot2docker/profile

### 拉取镜像
docker pull openstf/stf:latest
docker pull sorccu/adb:latest
docker pull rethinkdb:latest
docker pull openstf/ambassador:latest
docker pull nginx:latest

### 启动容器

	#先启动一个数据库
	docker run -d --name rethinkdb -v /srv/rethinkdb:/data --net host rethinkdb rethinkdb --bind all --cache-size 2048 --http-port 8090  
	
	#再启动adb service
	docker run -d --name adbd --privileged -v /dev/bus/usb:/dev/bus/usb --net host sorccu/adb:latest
	
	#再启动stf
	docker run -d --name stf --net host openstf/stf stf local --public-ip 192.168.99.100 --allow-remote

	stf provider --name PC-31010 --min-port 7400 --max-port 7700 --connect-sub tcp://127.0.0.1:7114 --connect-push tcp://127.0.0.1:7116 --group-timeout 20000 --public-ip 192.168.99.100 --storage-url http://localhost:7100/ --adb-host 192.168.99.1 --adb-port 5037 --vnc-initial-size 600x800 --allow-remote


### adb暴露端口
adb 1.0.39 不能使用 adb fork-server 

通过adb nodaemon server -a运行adb服务
adb -a -P 5037 server nodaemon

adb -a -P 5037 fork-server server


### jenkins
docker run -itd -p 18080:8080 -p 50000:50000 --name jenkins --privileged=true  -v /usr/local/docker/jenkins:/var/jenkins_home jenkins

	

### jenkins获取密码
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword