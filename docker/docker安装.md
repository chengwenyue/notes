### Docker Toolbox Windows安装工具内容

	1. Docker Clinet for Windows 必选
	2. Docker Machine for Windows 必选
	3. Docker Compose for Windows 可选
	4. VirtualBox  必选
	5. Kitematic for Windows 可选
	6. Git for Windows 可选


### docker 加速器网址

	官方加速器网址
	https://registry.docker-cn.com/


```
 yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
 
 
 sudo yum install -y yum-utils
 
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
    

yum list docker-ce --showduplicates | sort -r


sudo yum install docker-ce-20.10.22-3.el7 containerd.io docker-compose-plugin

sudo yum install  docker-ce-cli-18.09.3-3.el7


sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io docker-buildx-plugin docker-compose-plugin


```



删除docker

```

rpm -qa | grep docker

yum remove docker-scan-plugin-0.23.0-3.el7.x86_64 docker-ce-cli-20.10.22-3.el7.x86_64 docker-compose-plugin-2.14.1-3.el7.x86_64 docker-ce-rootless-extras-20.10.22-3.el7.x86_64 docker-ce-19.03.9-3.el7.x86_64


```



