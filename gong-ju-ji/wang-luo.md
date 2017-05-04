## 查看

ip a

ethtool eth0

arping -I eth0 192.168.0.10



ping -I eth2 192.168.0.150//通过指定端口eth2来ping192.168.0.150这个地址。

查看两个网口是否连载同一个交换机上: 看他ping过去有几跳



查看流量的工具：vnstat , sar, ntop

vnstat -i em1 -l

sar -n DEV 1

ntop

用tcpreplay发送DNS报文时，由于：

1. DNS报文平均100Bytes
2. 每个网卡可处理的吞吐率有限，比如最大达到300万个包/s。

那么，无论如何在一台机器上并发执行几个tcpreplay，统一网卡的发送流量最大就是3Gbps。

潜在解决方案：在多台机器上并发执行tcpreplay，这样就不会被一块网卡的吞吐量给限制住。

