## win

### win10端口转发

1、添加端口转发

	netsh interface portproxy add v4tov4 listenport=4000 listenaddress=127.0.0.1 connectport=4000 connectaddress=172.31.217.198
2、删除端口转发

	netsh interface portproxy del v4tov4 listenport=4000 listenaddress=127.0.0.1
3、查看已存在的端口映射

	netsh interface portproxy show v4tov4

可以通过命令 netstat -ano|find 4000 查看端口是否已在监听

	telnet 127.0.0.1 4000 测试端口是否连通
