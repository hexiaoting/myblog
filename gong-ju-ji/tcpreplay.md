# Tcpreplay用于发包的工具

常用命令

```
./tcpreplay -i em1 -t -K -l 2 --unique-ip /home/hewenting/ts01-cap_dns_v6.pcap
```

> -i:  Client to server/RX/primary traffic output interface指定输出的网卡  
> -t: topspeed, Replay packets as fast as possible 尽可能快的发包
>
> -K: --preload-pcap         Preloads packets into RAM before sending先加载到内存
>
> -l: --loop=num             Loop through the capture file X times循环次数
>
> --unique-ip:  Modify IP addresses each loop iteration to generate unique flows每次循环发包改变ip地址
>
> -L, --limit=num            Limit the number of packets to send 限制发包个数
>
> -p: --pps=num              Replay packets at a given packets/sec指定每秒的发包速度
>
> -M, --mbps=str             Replay packets at a given Mbps指定发包速率

例子1：

```
[root@nobida209 bin]# ./tcpreplay -i em1 -t -K /home/hewenting/ts01-cap_dns_v6.pcap
File Cache is enabled
Actual: 242 packets (33882 bytes) sent in 0.000244 seconds.
Rated: 138860600.0 Bps, 1110.88 Mbps, 991803.27 pps
Statistics for network device: em1
    Successful packets:        242
    Failed packets:            0
    Truncated packets:         0
    Retried packets (ENOBUFS): 0
    Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

例子2：循环发包，循环2次【-l 2】

```
[root@nobida209 bin]# ./tcpreplay -i em1 -t -K -l 2 /home/hewenting/ts01-cap_dns_v6.pcap
File Cache is enabled
Actual: 484 packets (67764 bytes) sent in 0.000357 seconds.
Rated: 189815100.0 Bps, 1518.52 Mbps, 1355742.29 pps
Statistics for network device: em1
    Successful packets:        484
    Failed packets:            0
    Truncated packets:         0
    Retried packets (ENOBUFS): 0
    Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

例子3：循环使得ip唯一【--unique-ip】

```
[root@nobida209 bin]# ./tcpreplay -i em1 -t -K -l 2 --unique-ip /home/hewenting/ts01-cap_dns_v6.pcap
File Cache is enabled
Actual: 484 packets (67764 bytes) sent in 0.000349 seconds.
Rated: 194166100.0 Bps, 1553.32 Mbps, 1386819.48 pps
Statistics for network device: em1
    Successful packets:        484
    Failed packets:            0
    Truncated packets:         0
    Retried packets (ENOBUFS): 0
    Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

例子4：限制发包个数【-L 2】

```
[root@nobida209 bin]# ./tcpreplay -i em1 -t -K -L 2  /home/hewenting/ts01-cap_dns_v6.pcap
File Cache is enabled
Actual: 2 packets (256 bytes) sent in 0.000010 seconds.
Rated: 25600000.0 Bps, 204.80 Mbps, 200000.00 pps
Statistics for network device: em1
    Successful packets:        2
    Failed packets:            0
    Truncated packets:         0
    Retried packets (ENOBUFS): 0
    Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

例子5：指定发包速度，每秒多少个包【-p 100】,这个参数和-t是冲突的

```
[root@nobida209 bin]# ./tcpreplay  -i em1  -K -p 100 -l 3  /home/hewenting/ts01-cap_dns_v6.pcap
File Cache is enabled
Actual: 726 packets (101646 bytes) sent in 7.02 seconds.
Rated: 14000.0 Bps, 0.112 Mbps, 100.13 pps
Statistics for network device: em1
    Successful packets:        726
    Failed packets:            0
    Truncated packets:         0
    Retried packets (ENOBUFS): 0
    Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

例子6：指定发包速度，Mbps【-M 1000】,这个参数和-t是冲突的

```
[root@nobida209 bin]# ./tcpreplay -i em1 -M 1000  /home/hewenting/pcap/ts01-cap_dns_v6.pcap
Actual: 242 packets (33882 bytes) sent in 0.000287 seconds.
Rated: 118055700.0 Bps, 944.44 Mbps, 843205.57 pps
Statistics for network device: em1
	Successful packets:        242
	Failed packets:            0
	Truncated packets:         0
	Retried packets (ENOBUFS): 0
	Retried packets (EAGAIN):  0
[root@nobida209 bin]#
```

## 待确定问题

> 1. tcpreplay发包不指定发往何处吗？还是会解析包里头的目的ip，然后发往这个目的ip?
> 2. 网上抓好的pcap包，如何在两个节点之间用tcpreplay传输？



