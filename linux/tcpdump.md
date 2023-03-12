# Linux抓包命令 TCPDump



tcpdump抓取 ping包

```shell
# -i 指定网卡
# -nn 不解析ip地址和端口号的名称

tcpdump -i ens33 icmp and host 192.168.5.1 -nn

```

tcpdump常用的参数

>-i 指定网卡
>
>-nn 不解析ip地址和端口号的名称
>
>-c  5  限制要抓取的网络包个数
>
>-w 保存到文件中，通常以pcap为后缀
>
>



tcpdump过滤表

| 选项                         | 示例                                        | 说明            |
| ---------------------------- | ------------------------------------------- | --------------- |
| host，src host ,dst host     | tcpdump -nn host 192.168.5.1                | 主机过滤        |
| port，src port , dst port    | tcpdump -nn port 80                         | 端口过滤        |
| ip，ip6，arp, tcp, udp, icmp | tcpdump -nn tcp                             | 协议过滤        |
| and , or , not               | tcpdump -nn host 192.168.5.1 and port 80    | 逻辑表达式      |
| tcp[tcpflages]               | tcpdump -nn "tcp[tcpflages] & tcp-syn != 0" | 特定状态的tcp包 |



## 抓取实例

### ping包抓取

抓取命令：

```shell
tcpdump -i ens33 icmp and host 192.168.5.1 -nn -w ping.pcap
```

另起一个窗口，然后在网卡ens33上执行 ping命令

```shell
ping -I ens33 -c 3 192.168.5.1
```



### tcp包抓取





