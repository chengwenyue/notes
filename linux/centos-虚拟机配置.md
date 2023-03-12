1. 设置静态Ip



```shell

vim /etc/sysconfig/network-scripts/ifcfg-*


TYPE=Ethernet
BOOTPROTO=static
DEFROUTE=yes
PEERDNS=yes
PEERROUTES=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_PEERDNS=yes
IPV6_PEERROUTES=yes
IPV6_FAILURE_FATAL=no
NAME=*
UUID=59af0c06-36ba-4019-b7f2-df967f4e8ab9
DEVICE=*
ONBOOT=yes
IPADDR=192.168.5.123
NETMASK=225.225.225.0
DNS1=8.8.8.8
DNS2=114.114.114.114
GATEWAY=192.168.5.2
```

**2、修改DNS配置**

```javascript
vim /etc/resolv.conf

    nameserver 8.8.8.8
    nameserver 114.114.114.114
```

**3、重启网络**

```shell
    service network restart
# or /etc/init.d/network restart

```



4.修改hostname

````shell
hostnamectl set-hostname m1
# or vim /etc/sysconfig/network

vim /etc/hosts 


````

