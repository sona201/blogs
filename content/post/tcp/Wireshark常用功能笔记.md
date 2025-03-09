---
title: "Wireshark常用功能笔记"
description: "wireshark笔记记录"
date: "2025-03-09T00:01:40+08:00"
lastmod: "2025-03-09T00:01:40+08:00"
image: 
categories: ["tcp"]
tags: ["tcp", "wireshark", "常用功能", "笔记"]
slug: 'Wireshark-Common-Function-Notes'
style:
    background: "#2a9d8f"
    color: "#fff"
---

[原文链接](https://www.ilikejobs.com/posts/wireshark/)

# Wireshark常用功能笔记


# wireshark

# 包类型

## pcap

**这个是 libpcap 的格式，也是 tcpdump 和 Wireshark 等工具默认支持的文件格式**。pcap 格 式的文件中除了报文数据以外，也包含了抓包文件的元信息，比如版本号、抓包时间、每个报 文被抓取的最大长度，等等。

## cap

**cap 文件可能含有一些 libpcap 标准之外的数据格式，它是由一些 tcpdump 以外的抓包程序生成的。** 比如 Citrix 公司的 netscaler 负载均衡器，它的 nstrace 命令生成的抓包文件，就是 以.cap 为扩展名的。这种文件除了包含 pcap 标准定义的信息以外，还包含了 LB 的前端连接 和后端连接之间的 mapping 信息。Wireshark 是可以读取这些.cap 文件的，只要在正确的 版本上

## pcapng

pcap 格式虽然满足了大部分需求，但是它也有一些不足。比如，现在多网口的情况已经越来 越常见了，我们也经常需要从多个网络接口去抓取报文，所以单个pcapng的包里面会包含多个网络接口的包。

![wireshark_pcapng1](/images/wireshark_pcapng1.png)

`pcapng 还有很多别的特性，比如更细粒度的报文时间戳、允许对报文添加注释、更灵活的元数据，如果你是用版本比较新的 Wireshark 和 tshark 做抓包，默认生成的抓包文件就已经是 pcapng 格式了`

# tcpdump

```bash
# tcpdump -i eth0 -s 80 -w /tmp/tcpdump.cap

- i 指定网卡eth0
- s 指定抓包大小80字节
- w 保持成文件存储位置

# tcpdump -i eth0 host 10.31.200.131 -w /tmp/tcpdump.cap

- host 表示只抓与10.31.200.131通信的包
- port 表示只抓指定端口的包

PS：加更多的限制可能会抓不到包，比如NAT把对方的包给改掉了。
```

## 常用参数

- -h：帮助

```bash
// On Linux
# tcpdump -h
[root@dev-env ~]# tcpdump -h
tcpdump version 4.9.2
libpcap version 1.5.3
OpenSSL 1.0.2k-fips  26 Jan 2017
Usage: tcpdump [-aAbdDefhHIJKlLnNOpqStuUvxX#] [ -B size ] [ -c count ]
                [ -C file_size ] [ -E algo:secret ] [ -F file ] [ -G seconds ]
                [ -i interface ] [ -j tstamptype ] [ -M secret ] [ --number ]
                [ -Q|-P in|out|inout ]
                [ -r file ] [ -s snaplen ] [ --time-stamp-precision precision ]
                [ --immediate-mode ] [ -T type ] [ --version ] [ -V file ]
                [ -w file ] [ -W filecount ] [ -y datalinktype ] [ -z postrotate-command ]
                [ -Z user ] [ expression ]
// On Mac
zhengxianle@zhengxinledembp  ~  dumpcap -h
Dumpcap (Wireshark) 4.0.5 (v4.0.5-0-ge556162d8da3)
Capture network packets and dump them into a pcapng or pcap file.
See https://www.wireshark.org for more information.

Usage: dumpcap [options] ...

Capture interface:
  -i <interface>, --interface <interface>
                           name or idx of interface (def: first non-loopback),
                           or for remote capturing, use one of these formats:
                               rpcap://<host>/<interface>
                               TCP@<host>:<port>
  --ifname <name>          name to use in the capture file for a pipe from which
                           we're capturing
  --ifdescr <description>
                           description to use in the capture file for a pipe
                           from which we're capturing
  -f <capture filter>      packet filter in libpcap filter syntax
```

- -D：查看有哪些网卡

```bash
// Linux
[root@dev-env ~]# tcpdump -D
1.eth0
2.tun0
3.nflog (Linux netfilter log (NFLOG) interface)
4.nfqueue (Linux netfilter queue (NFQUEUE) interface)
5.usbmon1 (USB bus number 1)
6.any (Pseudo-device that captures on all interfaces)
7.lo [Loopback]
// MacOS
zhengxianle@zhengxinledembp  ~#  dumpcap -D
1. en0 (Wi-Fi)
2. awdl0
3. llw0
4. utun0
5. utun1
6. utun2
7. utun3
8. utun4
9. utun5
10. utun6
11. utun7
12. utun8
13. utun9
14. lo0 (Loopback)
15. en4 (Thunderbolt 4)
16. en1 (Thunderbolt 1)
17. en2 (Thunderbolt 2)
18. en3 (Thunderbolt 3)
19. bridge0 (Thunderbolt Bridge)
20. pktap0
21. gif0
22. stf0
23. ap1
zhengxianle@zhengxinledembp~# tcpdump -D
1.en0 [Up, Running, Wireless, Associated]
2.awdl0 [Up, Running, Wireless, Associated]
3.llw0 [Up, Running, Wireless, Associated]
4.utun0 [Up, Running]
5.utun1 [Up, Running]
6.utun2 [Up, Running]
7.utun3 [Up, Running]
8.utun4 [Up, Running]
9.utun5 [Up, Running]
10.utun6 [Up, Running]
11.utun7 [Up, Running]
12.utun8 [Up, Running]
13.utun9 [Up, Running]
14.lo0 [Up, Running, Loopback]
15.en4 [Up, Running, Disconnected]
16.en1 [Up, Running, Disconnected]
17.en2 [Up, Running, Disconnected]
18.en3 [Up, Running, Disconnected]
19.bridge0 [Up, Running, Disconnected]
20.pktap0 [Up]
21.gif0 [none]
22.stf0 [none]
23.ap1 [Wireless, Association status unknown]
```

- -i : 指定网卡，这里1指定的是-D中的网卡序号

```bash
zhengxianle@zhengxinledembp~ # dumpcap -i 1
Capturing on 'Wi-Fi: en0'
File: /var/folders/23/7d2bd6ss2db4vjz8twqwf4940000gn/T/wireshark_Wi-FiSI0731.pcapng
Packets captured: 923
Packets received/dropped on interface 'Wi-Fi: en0': 923/0 (pcap:0/dumpcap:0/flushed:0/ps_ifdrop:0) (100.0%)
```

- -w：指定存储在的位置

```bash
zhengxianle@zhengxinledembp~# dumpcap -i 1 -w /Users/zhengxianle/Desktop/capture/sample.pcapng
Capturing on 'Wi-Fi: en0'
File: /Users/zhengxianle/Desktop/capture/sample.pcapng
Packets captured: 573
Packets received/dropped on interface 'Wi-Fi: en0': 573/0 (pcap:0/dumpcap:0/flushed:0/ps_ifdrop:0) (100.0%)
```

- -b
    - filesize:500000 // 代表500M
    - files:10 // 代表一共存10个文件，当第11个文件产生时，会覆盖第一个文件。

```bash
更详细的配置：
-b <ringbuffer opt.> ..., --ring-buffer <ringbuffer opt.>
                           duration:NUM - switch to next file after NUM secs
                           filesize:NUM - switch to next file after NUM kB
                              files:NUM - ringbuffer: replace after NUM files
                            packets:NUM - ringbuffer: replace after NUM packets
                           interval:NUM - switch to next file when the time is
                                          an exact multiple of NUM secs
                          printname:FILE - print filename to FILE when written
                                           (can use 'stdout' or 'stderr')
```

```bash
zhengxianle@zhengxinledembp~ # dumpcap -i 1 -w /Users/zhengxianle/Desktop/capture/sample.pcapng -b filesize:500000
 -b files:10
Capturing on 'Wi-Fi: en0'
File: /Users/zhengxianle/Desktop/capture/sample_00001_20230426220541.pcapng
Packets captured: 1238
Packets received/dropped on interface 'Wi-Fi: en0': 1238/0 (pcap:0/dumpcap:0/flushed:0/ps_ifdrop:0) (100.0%)
```

- -c ： 数量，可以抓取固定数量的保文，在流量较高时，可以避免一不小心抓取过多报文
    
- -n：不做地址转换（比如IP地址转换为主机，port 80转换为http）
    
- -v/-vv/-vvv:可以打印更加详细的报文信息
    
- -e ： 可以打印二层信息，特别是MAC地址
    
- -p：关闭混杂模式。
    
    `所谓混杂模式，也就是嗅探（Sniffing），就是把目的地址不是本机地址的网络报文也抓取下来。`
    
- -X ：在解析和打印时，除了打印每个数据包的头之外，还可以用十六进制和ASCII打印每个数据包的数据（减去其链接级头）。 这对于分析新协议非常方便。 在目前的实现中，如果数据包被截断，这个标志可能与-XX的效果相同。
    

`这个参数很有用啊，可以看到具体的请求内容了，快速排查问题的时候是可以这么做的。`

```bash
[root@dev-env ~]# netstat -ntpl
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 0.0.0.0:41046           0.0.0.0:*               LISTEN      3953/java           
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1957/sshd           
tcp        0      0 0.0.0.0:8089            0.0.0.0:*               LISTEN      2818/nginx: master  
tcp        0      0 0.0.0.0:8090            0.0.0.0:*               LISTEN      2818/nginx: master  
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      2818/nginx: master  
tcp        0      0 0.0.0.0:8091            0.0.0.0:*               LISTEN      2818/nginx: master  
tcp        0      0 0.0.0.0:18080           0.0.0.0:*               LISTEN      3953/java           
tcp        0      0 0.0.0.0:10051           0.0.0.0:*               LISTEN      2171/zabbix_proxy   
tcp        0      0 0.0.0.0:11940           0.0.0.0:*               LISTEN      1492/openvpn        
tcp        0      0 0.0.0.0:2181            0.0.0.0:*               LISTEN      3953/java           
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      1837/mysqld         
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1138/rpcbind        
tcp6       0      0 :::10050                :::*                    LISTEN      1489/zabbix_agent2  
tcp6       0      0 :::10051                :::*                    LISTEN      2171/zabbix_proxy   
tcp6       0      0 :::111                  :::*                    LISTEN      1138/rpcbind        
[root@dev-env ~]# tcpdump -i any -X port 18080
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
22:02:52.188802 IP 10.222.1.77.51848 > 172.22.183.239.18080: Flags [.], ack 3220596691, win 790, options [nop,nop,TS val 2719039184 ecr 2964196926], length 0
        0x0000:  4500 0034 2a37 4000 8006 605c 0ade 014d  E..4*7@...`\...M
        0x0010:  ac16 b7ef ca88 46a0 8504 150d bff6 67d3  ......F.......g.
        0x0020:  8010 0316 90a3 0000 0101 080a a211 3ed0  ..............>.
        0x0030:  b0ae 0e3e 0000 0000 0000 0000 0000 0000  ...>............
        0x0040:  0000 0000                                ....
22:02:52.188822 IP 10.222.1.77.51848 > 172.22.183.239.18080: Flags [.], ack 1, win 790, options [nop,nop,TS val 2719039184 ecr 2964196926], length 0
        0x0000:  4500 0034 2a37 4000 7f06 615c 0ade 014d  E..4*7@...a\...M
        0x0010:  ac16 b7ef ca88 46a0 8504 150d bff6 67d3  ......F.......g.
        0x0020:  8010 0316 90a3 0000 0101 080a a211 3ed0  ..............>.
        0x0030:  b0ae 0e3e 0000 0000 0000 0000 0000 0000  ...>............
        0x0040:  0000 0000                                ....
22:02:52.188961 IP 172.22.183.239.18080 > 10.222.1.77.51848: Flags [.], ack 1, win 805, options [nop,nop,TS val 2964201950 ecr 2719019098], length 0
        0x0000:  4500 0034 9a5b 4000 8006 f037 ac16 b7ef  E..4.[@....7....
        0x0010:  0ade 014d 46a0 ca88 bff6 67d3 8504 150e  ...MF.....g.....
        0x0020:  8010 0325 cb69 0000 0101 080a b0ae 21de  ...%.i........!.
        0x0030:  a210 f05a 0000 0000 0000 0000 0000 0000  ...Z............
        0x0040:  0000 0000                                ....
22:02:52.188968 IP 172.22.183.239.18080 > 10.222.1.77.51848: Flags [.], ack 1, win 805, options [nop,nop,TS val 2964201950 ecr 2719019098], length 0
        0x0000:  4500 0034 9a5b 4000 7f06 f137 ac16 b7ef  E..4.[@....7....
        0x0010:  0ade 014d 46a0 ca88 bff6 67d3 8504 150e  ...MF.....g.....
        0x0020:  8010 0325 cb69 0000 0101 080a b0ae 21de  ...%.i........!.
        0x0030:  a210 f05a 0000 0000 0000 0000 0000 0000  ...Z............
        0x0040:  0000 0000                                ....
22:02:57.212632 IP 10.222.1.77.51848 > 172.22.183.239.18080: Flags [.], ack 1, win 790, options [nop,nop,TS val 2719044208 ecr 2964201950], length 0
        0x0000:  4500 0034 2a38 4000 8006 605b 0ade 014d  E..4*8@...`[...M
        0x0010:  ac16 b7ef ca88 46a0 8504 150d bff6 67d3  ......F.......g.
        0x0020:  8010 0316 6963 0000 0101 080a a211 5270  ....ic........Rp
        0x0030:  b0ae 21de 0000 0000 0000 0000 0000 0000  ..!.............
        0x0040:  0000 0000                                ....
22:02:57.212652 IP 10.222.1.77.51848 > 172.22.183.239.18080: Flags [.], ack 1, win 790, options [nop,nop,TS val 2719044208 ecr 2964201950], length 0
        0x0000:  4500 0034 2a38 4000 7f06 615b 0ade 014d  E..4*8@...a[...M
        0x0010:  ac16 b7ef ca88 46a0 8504 150d bff6 67d3  ......F.......g.
        0x0020:  8010 0316 6963 0000 0101 080a a211 5270  ....ic........Rp
        0x0030:  b0ae 21de 0000 0000 0000 0000 0000 0000  ..!.............
        0x0040:  0000 0000                                ....
22:02:57.212782 IP 172.22.183.239.18080 > 10.222.1.77.51848: Flags [.], ack 1, win 805, options [nop,nop,TS val 2964206974 ecr 2719019098], length 0
        0x0000:  4500 0034 9a5c 4000 8006 f036 ac16 b7ef  E..4.\@....6....
        0x0010:  0ade 014d 46a0 ca88 bff6 67d3 8504 150e  ...MF.....g.....
        0x0020:  8010 0325 b7c9 0000 0101 080a b0ae 357e  ...%..........5~
        0x0030:  a210 f05a 0000 0000 0000 0000 0000 0000  ...Z............
        0x0040:  0000 0000                                ....
22:02:57.212790 IP 172.22.183.239.18080 > 10.222.1.77.51848: Flags [.], ack 1, win 805, options [nop,nop,TS val 2964206974 ecr 2719019098], length 0
        0x0000:  4500 0034 9a5c 4000 7f06 f136 ac16 b7ef  E..4.\@....6....
        0x0010:  0ade 014d 46a0 ca88 bff6 67d3 8504 150e  ...MF.....g.....
        0x0020:  8010 0325 b7c9 0000 0101 080a b0ae 357e  ...%..........5~
        0x0030:  a210 f05a 0000 0000 0000 0000 0000 0000  ...Z............
        0x0040:  0000 0000                                ....
22:02:59.585608 IP 10.222.1.12.57689 > 172.22.183.239.18080: Flags [P.], seq 1350914016:1350914568, ack 1773723314, win 2048, options [nop,nop,TS val 3234104033 ecr 2964179285], length 552
        0x0000:  4502 025c 0000 4000 4006 c8aa 0ade 010c  E..\..@.@.......
        0x0010:  ac16 b7ef e159 46a0 5085 4fe0 69b8 deb2  .....YF.P.O.i...
        0x0020:  8018 0800 f5cf 0000 0101 080a c0c4 82e1  ................
        0x0030:  b0ad c955 504f 5354 202f 7878 6c2d 6a6f  ...UPOST./xxl-jo
        0x0040:  622d 6164 6d69 6e2f 6170 6920 4854 5450  b-admin/api.HTTP
        0x0050:  2f31 2e31 0d0a 686f 7374 3a20 3137 322e  /1.1..host:.172.
        0x0060:  3232 2e31 3833 2e32 3339 0d0a 636f 6e6e  22.183.239..conn
        0x0070:  6563 7469 6f6e 3a20 6b65 6570 2d61 6c69  ection:.keep-ali
        0x0080:  7665 0d0a 636f 6e74 656e 742d 6c65 6e67  ve..content-leng
        0x0090:  7468 3a20 3434 390d 0a0d 0a43 302d 636f  th:.449....C0-co
        0x00a0:  6d2e 7878 6c2e 7270 632e 7265 6d6f 7469  m.xxl.rpc.remoti
        0x00b0:  6e67 2e6e 6574 2e70 6172 616d 732e 5878  ng.net.params.Xx
        0x00c0:  6c52 7063 5265 7175 6573 7498 0972 6571  lRpcRequest..req
        0x00d0:  7565 7374 4964 1063 7265 6174 654d 696c  uestId.createMil
        0x00e0:  6c69 7354 696d 650b 6163 6365 7373 546f  lisTime.accessTo
        0x00f0:  6b65 6e09 636c 6173 734e 616d 650a 6d65  ken.className.me
        0x0100:  7468 6f64 4e61 6d65 0776 6572 7369 6f6e  thodName.version
        0x0110:  0e70 6172 616d 6574 6572 5479 7065 730a  .parameterTypes.
        0x0120:  7061 7261 6d65 7465 7273 6030 2432 6332  parameters`0$2c2
        0x0130:  3332 3739 382d 3536 6164 2d34 3562 362d  32798-56ad-45b6-
        0x0140:  6162 3333 2d61 6665 6636 6261 3630 3565  ab33-afef6ba605e
        0x0150:  334c 0000 0188 4e11 f43a 4e1d 636f 6d2e  3L....N..:N.com.
        0x0160:  7878 6c2e 6a6f 622e 636f 7265 2e62 697a  xxl.job.core.biz
        0x0170:  2e41 646d 696e 4269 7a08 7265 6769 7374  .AdminBiz.regist
        0x0180:  7279 4e71 105b 6a61 7661 2e6c 616e 672e  ryNq.[java.lang.
        0x0190:  436c 6173 7343 0f6a 6176 612e 6c61 6e67  ClassC.java.lang
        0x01a0:  2e43 6c61 7373 9104 6e61 6d65 6130 2863  .Class..namea0(c
        0x01b0:  6f6d 2e78 786c 2e6a 6f62 2e63 6f72 652e  om.xxl.job.core.
        0x01c0:  6269 7a2e 6d6f 6465 6c2e 5265 6769 7374  biz.model.Regist
        0x01d0:  7279 5061 7261 6d71 075b 6f62 6a65 6374  ryParamq.[object
        0x01e0:  4330 2863 6f6d 2e78 786c 2e6a 6f62 2e63  C0(com.xxl.job.c
        0x01f0:  6f72 652e 6269 7a2e 6d6f 6465 6c2e 5265  ore.biz.model.Re
        0x0200:  6769 7374 7279 5061 7261 6d93 0b72 6567  gistryParam..reg
        0x0210:  6973 7447 726f 7570 0b72 6567 6973 7472  istGroup.registr
        0x0220:  794b 6579 0d72 6567 6973 7472 7956 616c  yKey.registryVal
        0x0230:  7565 6208 4558 4543 5554 4f52 0e6f 7370  ueb.EXECUTOR.osp
        0x0240:  4461 7461 4d61 702d 6a6f 6210 3130 2e32  DataMap-job.10.2
        0x0250:  3136 2e31 2e31 313a 3939 3939 0000 0000  16.1.11:9999....
        0x0260:  0000 0000 0000 0000 0000 0000            ............
22:02:59.585641 IP 10.222.1.12.57689 > 172.22.183.239.18080: Flags [P.], seq 0:552, ack 1, win 2048, options [nop,nop,TS val 3234104033 ecr 2964179285], length 552
        0x0000:  4502 025c 0000 4000 3f06 c9aa 0ade 010c  E..\..@.?.......
        0x0010:  ac16 b7ef e159 46a0 5085 4fe0 69b8 deb2  .....YF.P.O.i...
        0x0020:  8018 0800 f5cf 0000 0101 080a c0c4 82e1  ................
        0x0030:  b0ad c955 504f 5354 202f 7878 6c2d 6a6f  ...UPOST./xxl-jo
        0x0040:  622d 6164 6d69 6e2f 6170 6920 4854 5450  b-admin/api.HTTP
        0x0050:  2f31 2e31 0d0a 686f 7374 3a20 3137 322e  /1.1..host:.172.
        0x0060:  3232 2e31 3833 2e32 3339 0d0a 636f 6e6e  22.183.239..conn
        0x0070:  6563 7469 6f6e 3a20 6b65 6570 2d61 6c69  ection:.keep-ali
        0x0080:  7665 0d0a 636f 6e74 656e 742d 6c65 6e67  ve..content-leng
        0x0090:  7468 3a20 3434 390d 0a0d 0a43 302d 636f  th:.449....C0-co
        0x00a0:  6d2e 7878 6c2e 7270 632e 7265 6d6f 7469  m.xxl.rpc.remoti
        0x00b0:  6e67 2e6e 6574 2e70 6172 616d 732e 5878  ng.net.params.Xx
        0x00c0:  6c52 7063 5265 7175 6573 7498 0972 6571  lRpcRequest..req
        0x00d0:  7565 7374 4964 1063 7265 6174 654d 696c  uestId.createMil
        0x00e0:  6c69 7354 696d 650b 6163 6365 7373 546f  lisTime.accessTo
        0x00f0:  6b65 6e09 636c 6173 734e 616d 650a 6d65  ken.className.me
        0x0100:  7468 6f64 4e61 6d65 0776 6572 7369 6f6e  thodName.version
        0x0110:  0e70 6172 616d 6574 6572 5479 7065 730a  .parameterTypes.
        0x0120:  7061 7261 6d65 7465 7273 6030 2432 6332  parameters`0$2c2
        0x0130:  3332 3739 382d 3536 6164 2d34 3562 362d  32798-56ad-45b6-
        0x0140:  6162 3333 2d61 6665 6636 6261 3630 3565  ab33-afef6ba605e
        0x0150:  334c 0000 0188 4e11 f43a 4e1d 636f 6d2e  3L....N..:N.com.
        0x0160:  7878 6c2e 6a6f 622e 636f 7265 2e62 697a  xxl.job.core.biz
        0x0170:  2e41 646d 696e 4269 7a08 7265 6769 7374  .AdminBiz.regist
        0x0180:  7279 4e71 105b 6a61 7661 2e6c 616e 672e  ryNq.[java.lang.
        0x0190:  436c 6173 7343 0f6a 6176 612e 6c61 6e67  ClassC.java.lang
        0x01a0:  2e43 6c61 7373 9104 6e61 6d65 6130 2863  .Class..namea0(c
        0x01b0:  6f6d 2e78 786c 2e6a 6f62 2e63 6f72 652e  om.xxl.job.core.
        0x01c0:  6269 7a2e 6d6f 6465 6c2e 5265 6769 7374  biz.model.Regist
        0x01d0:  7279 5061 7261 6d71 075b 6f62 6a65 6374  ryParamq.[object
        0x01e0:  4330 2863 6f6d 2e78 786c 2e6a 6f62 2e63  C0(com.xxl.job.c
        0x01f0:  6f72 652e 6269 7a2e 6d6f 6465 6c2e 5265  ore.biz.model.Re
        0x0200:  6769 7374 7279 5061 7261 6d93 0b72 6567  gistryParam..reg
        0x0210:  6973 7447 726f 7570 0b72 6567 6973 7472  istGroup.registr
        0x0220:  794b 6579 0d72 6567 6973 7472 7956 616c  yKey.registryVal
        0x0230:  7565 6208 4558 4543 5554 4f52 0e6f 7370  ueb.EXECUTOR.osp
        0x0240:  4461 7461 4d61 702d 6a6f 6210 3130 2e32  DataMap-job.10.2
        0x0250:  3136 2e31 2e31 313a 3939 3939 0000 0000  16.1.11:9999....
        0x0260:  0000 0000 0000 0000 0000 0000            ............
```

# 抓包技巧

## 打标记

包除了小以外，还需要为每步操作打标记，这样的包一目了然，赏心悦目。比如：

1. sudo ping [www.baidu.com](http://www.baidu.com/) -c 1 -s 1
2. 操作步骤1
3. sudo ping [www.baidu.com](http://www.baidu.com/) -c 1 -s 2
4. 操作步骤2
5. sudo ping [www.baidu.com](http://www.baidu.com/) -c 1 -s 3
6. 操作步骤3

> 解释（MacOS下）：-c 指定ping次数，-s指定包大小

```bash
usage: ping [-AaDdfnoQqRrv] [**-c count**] [-G sweepmaxsize]
            [-g sweepminsize] [-h sweepincrsize] [-i wait]
            [-l preload] [-M mask | time] [-m ttl] [-p pattern]
            [-S src_addr] [**-s packetsize**] [-t timeout][-W waittime]
            [-z tos] host
       ping [-AaDdfLnoQqRrv] [-c count] [-I iface] [-i wait]
            [-l preload] [-M mask | time] [-m ttl] [-p pattern] [-S src_addr]
            [-s packetsize] [-T ttl] [-t timeout] [-W waittime]
            [-z tos] mcast-group
Apple specific options (to be specified before mcast-group or host like all options)
            -b boundif           # bind the socket to the interface
            -k traffic_class     # set traffic class socket option
            -K net_service_type  # set traffic class socket options
            --apple-connect       # call connect(2) in the socket
            --apple-time          # display current time
```

在抓包时可以看到包下面Data（-s部分大小），byte的大小表示当前是第几步，就不容易出错。

![wireshark_pcapng2](/images/wireshark_pcapng2.png)

## 过滤技巧

- 已知协议，直接输入协议即可，例如：http/tcp/smtp等，多个协议可以用||来分开，例如：tcp||http
- ip+端口号是比较常用的过滤方式，例如：ip.addr eq 172.22.183.226 && tcp.port eq 8080，还可以右键单击感兴趣的包，选择follow TCP/UDP Stream，就可以自动过滤当前的包了。
- 过滤跟某个IP地址相关的包

```bash
ip.addr == 192.168.0.1
```

- 排除某些端口

```bash
not (tcp or arp or smtp)
```

- 根据端口范围查找

```bash
tcp.port in {80,443,8080}
```

- 按照关键词查询：区分大小写

```bash
frame contains "baidu.com"
```

![wireshark_pcapng3](/images/wireshark_pcapng3.png)

- 按照关键词查询：不区分大小写（matches采用的是regex）

```bash
frame matches "baidu.com"
```

![wireshark_pcapng4](/images/wireshark_pcapng4.png)

**如何过滤一个TCP/UDP Stream呢？**

两端的IP加port，单击wireshark的Statistics→conversations，再单击TCP或者UDP标签就可以看到所有的Stream了。如下图：

![wireshark_pcapng5](/images/wireshark_pcapng5.png)

- 用鼠标过滤：可以选择感兴趣的包，右键加入筛选即可。有两种方式：
    
    - Prepare a Filter→Selected（可以选择多个一起执行）
    - Apply as Filter→Selected（立即自动执行）
    
    也有很多组合方式可以按条件来：
    
    - Selected
    - not selected
    - and selected
    - or selected
    - and not selected
    - or not selected
- 过滤后的导出到新文件，因为文件更小。导出时要选择File→Export Specified Packets，这样就不会漏掉关联的包，不然会报错。
    
- 过滤失败的握手
    
```bash
tcp.flags.reset == 1 && tcp.seq==1
```
    
- 过滤重传的握手请求
    

过滤完查看完整的包流程，可以看到服务端的端口被使用了。

```bash
tcp.flags.syn == 1 && tcp.analysis.retransmission
```

![wireshark_pcapng6](/images/wireshark_pcapng6.png)

## 抓包时过滤

需求：统计某个HTTPS VIP的访问流量里，TLS版本（主要是TLS1.0、1.1、1.2、1.3）的分布。为了控制抓包文件的大小，不抓TLS的所有报文，只想抓TLS版本信息。

针对这个需求，tcpdump本身没有现成的过滤器。BPF本身是基于偏移量来做解析的，所以可以在tcpdump中使用这种偏移量技术，所以类似如下命令：

```bash
tcpdump -w /tmp/[fileter_TLS_Client_Hello.pcap](https://drive.google.com/file/d/112X8cKH78DQ0GQL8kMlT2RvI3gxUwbHv/view?usp=drive_link) 'dst port 443 && tcp[20]==22 && tcp[25]==1'
```

- dst port 443：这个最简单，就是抓取从客户端发过来的访问 HTTPS 的报文。
- tcp[20]==22：这是提取了 TCP 的第 21 个字节（因为初始序号是从 0 开始的），由于 TCP 头部占 20 字节，TLS 又是 TCP 的载荷，那么 TLS 的第 1 个字节就是 TCP 的第 21 个字节，也就是 TCP[20]，这个位置的值如果是 22（十进制），那么就表明这个是 TLS 握手报文。
- tcp[25]==1：同理，这是 TCP 头部的第 26 个字节，如果它等于 1，那么就表明这个是 Client Hello 类型的 TLS 握手报文。

![wireshark_pcapng7](/images/wireshark_pcapng7.png)

对应到偏移量的介绍如下：

![wireshark_pcapng8](/images/wireshark_pcapng8.png)

tcpdump 也预定义了一些相对方便的过滤器。比如我们想要过滤出 TCP RST 报文，那么可以用下面这种写法，相对来说比用数字做偏移量的写法，要更加容易理解和记忆：

```bash
tcpdump -w file.pcap 'tcp[tcpflags]&(tcp-rst) != 0'
```

对应到偏移量的写法如下：

```bash
tcpdump -w file.pcap 'tcp[13]&4 != 0'
```

## 读取抓包文件

tcpdump后面加上-r参数和文件名称，就可以读取这个文件了，也可以加上过滤条件：

```bash
tcpdump -r file.pcap 'tcp[tcpflags] & (tcp-rst) != 0'
```

## 过滤后转存

抓包以后过滤想要的报文，并保存到另一个文件中，如下所示：

```bash
tcpdump -r file.pcap 'tcp[tcpflags]&(tcp-rst)!=0' -w rst.pcap
```

# 配置

## 解析已知协议与IP域名映射

![wireshark_pcapng9](/images/wireshark_pcapng9.png)

## 查询当前已经解析了哪些域名

![wireshark_pcapng10](/images/wireshark_pcapng10.png)

## 设置私有IP名称

![wireshark_pcapng11](/images/wireshark_pcapng11.png)

查看刚设置自定义的名称：

![wireshark_pcapng12](/images/wireshark_pcapng12.png)

## 保存文件（含host对应名称）

![wireshark_pcapng13](/images/wireshark_pcapng13.png)

## 设置时间列

设置计时器，按照发送包的时间间隔来设置当前包发出去后返回需要的时间。

![wireshark_pcapng14](/images/wireshark_pcapng14.png)

设置时间重新开始计时的时间引用，时间会从当前引用开始重新计时

![wireshark_pcapng15](/images/wireshark_pcapng15.png)

设置TCP Stream Time，tcp的响应时间（可以根据此时间做排序以后排查慢的包）

![wireshark_pcapng16](/images/wireshark_pcapng16.png)

# 自动分析

1. 单击Analyze→Expert Information，可以显示出来不同级别的提示信息。比如：重传的统计、连接的建立和重置统计等。分析网络性能和连接问题时这个功能比较有用，如下图：

![wireshark_pcapng17](/images/wireshark_pcapng17.png)

1. 单击Statistics→Service Response Time，再选定协议名称，可以得到响应时间统计表。在衡量服务器性能时经常需要统计此结果。如下图：选择的是SMB协议的操作响应时间，**不知道为啥没有值。**
    
![wireshark_pcapng18](/images/wireshark_pcapng18.png)

2. 单击Statistics→TCP Stream Graph，可以生成几类统计图。例如：
    
- Time-Sequence Graph（Stevens）
- Time-Sequence Graph（tcptrace）
- Throughput
- Round Trip Time
- Window Scaling
    
![wireshark_pcapng19](/images/wireshark_pcapng19.png)
    

可以根据传输数据信息来分析系统发送数据状态。

1. 单击Statistics→ Capture File Properties看到一些统计信息，比如平均流量等，可以推测负载状况。如下图：流量大小6.8M。

![wireshark_pcapng20](/images/wireshark_pcapng20.png)

1. 概览（statistics→Conversations）,根据此可以分析出当前抓包的概览信息，包括

- 包的数量，大小IPv4流量信息，传输时间长
- TCP中开始时间，数据传输时间
- TCP中IP地址，端口号，包的大小，包开始时间，持续时间
- UDP的客户端、服务端信息，传输包的大小，开始时间，持续时间等信息

![wireshark_pcapng21](/images/wireshark_pcapng21.png)

可以根据需求，直接使用右键进行包的过滤，过滤以后可以在主页面中看到过滤后的包。

`注意：当开启Nagle算法和延迟确认时，在分析过程中如果看不出差异，可以缩小时间范围。`

# 文件提取（未加密的数据）

设置允许重组tcp流量包

![wireshark_pcapng22](/images/wireshark_pcapng22.png)

导出http文件（这里是http的请求）

![wireshark_pcapng23](/images/wireshark_pcapng23.png)

选择以后，点击保存即可

![wireshark_pcapng24](/images/wireshark_pcapng24.png)

# [IP地址映射（GeoIP）](https://www.youtube.com/watch?v=IlVppluWTHw)

可以在此处下载IP地址对应的城市：[https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en](https://dev.maxmind.com/geoip/geolite2-free-geolocation-data?lang=en)

![wireshark_pcapng25](/images/wireshark_pcapng25.png)

可以在statistics→endpoints中进行分析，同时可以用map进行映射到地图

![wireshark_pcapng26](/images/wireshark_pcapng26.png)

根据城市做搜索：

![wireshark_pcapng27](/images/wireshark_pcapng27.png)

# 搜索

command+f搜索关键字，例如查询“common/service”的关键词，如下图：**这个太有用了！！**

![wireshark_pcapng28](/images/wireshark_pcapng28.png)

# 查看走哪个网卡

```bash
# ip route get 172.16.0.52
172.16.0.52 dev **en0**  src 172.16.0.52
# ip route get develop.pansft.com
172.22.183.226 via 10.222.1.1 dev **utun8**  src 10.222.1.4
# ip route get www.ilikejobs.com
104.21.42.70 via 172.16.0.1 dev **en0**  src 172.16.0.52
```

# TShark

[**tshark**](https://www.notion.so/tshark-de1f0be16cd0480c990e894b44fe1dec?pvs=21)

# Layout

## 设置窗口的Layout

这里设置第三个窗口，以协议栈的方式显示。

![wireshark_pcapng29](/images/wireshark_pcapng29.png)

右键可以显示当前每个协议字段的值：

![wireshark_pcapng30](/images/wireshark_pcapng30.png)

设置完了显示如此：

![wireshark_pcapng31](/images/wireshark_pcapng31.png)

# 安全

## DDoS之SYN flood

原理，大量主机发送SYN请求给服务器，假装要链接TCP建立链接。这些SYN请求可能含有假的源地址，所以服务器响应以后永远收不到Ack，就会留下half-open状态的TCP链接。由于每个TCP链接都会消耗一定的系统资源，所以大量的无用连接会影响到真正的用户访问，导致拒绝访问情况产生。

# 常见错误

## TCP window full

表示这个包的发送方已经把对方所声明的接收窗口耗尽了。

## TCP Zerowindow

TCP中的“win=”代表接收窗口的大小，表示这个包的**发送方**当前还有多少缓冲区可以接收数据。

上面这两个的区别：TCP window full表示这个包的发送方暂时没办法再发送数据了，TCP Zerowindow 表示这个包的发送方暂时没办法再接受数据了。也就是说两者都意味着传输暂停，都必须引起重视。

## Package size limited during capture

```bash
可以通过-s参数来指定抓包的大小，下面就是指定每个包抓1000字节的包。
[root] tcpdump -i eth0 -s 1000 -w /tmp/tcpdump.cap
```

## TCP Previous segment not captured

TCP传输过程中，同一台主机发出的数据段应该是连续的，即后一个包的Seq号等于前一个包的Seq+Len（三次握手和四次挥手是例外）。

如果wireshark发现一个包的seq号大于前一个包的seq+len，就知道中间缺失了一段数据。假如缺失的那段数据在整个网络包中都找不到（即排除乱序），就会提示[TCP Previous segment not captured]。

🔔：

网络包没有抓到分两种情况，一种是真丢了，另一种是实际上没有丢，但是被抓包工具漏掉了。

在Wireshark上如何区分这两种情况呢，只需要看对方回复的ACK即可，如果确认包含了没抓的那个包，那就是抓包工具漏掉了而已，否则就是真丢了。

`注意：网络传输路径中有可能网络设备MTU比较小，会导致丢包，需要保持网络路径的MTU一致。`

## TCP Acked unseen segment

当Wireshark发现被ACK的那个包没有抓到，就会提示TCP ACKED unseen segment，这个几乎是可以永远忽略的。

## TCP Out-of-order

TCP 在传输过程中（不包含三次握手，四次挥手），同一台主机发出的数据包应该是连续的，即最后一个包的Seq号等于前一个包的Seq+len。

小跨度的乱序影响不大，比如原本顺序 为1、2、3、4、5号包被打乱城2、1、3、4、5就没事，但跨度大的乱序可能触发快速重传，比如打乱城2、3、4、5、1时，就会触发足够多的Dup Ack，而导致1号包的重传。

## TCP Dup ACK

当乱序或者丢包发生时，接受方会收到一些Seq号比期望值大的包，它每次收到这种包就会Ack一次期望的Seq值，以此方式来提醒发送方，于是就产生了一些重复的ack，Wireshark会在这些重复的ack上打标记 [TCP Dup Ack]。

## TCP Fast Retransmission

当发送方收到3个以上或者TCP Dup ACK，就意味着之前发送的包可能丢了，于是快速重传它。

## TCP Retransmission

如果一个包真的丢了，有没有后续的包可以在接受方触发Dup Ack时，就不会快速重传，这种情况只能等到超时了再重传，此类包就会被Wireshark打上TCP Retransmission。

此处可以重点学习一下。

## TCP Segment of a reassembled PDU

这个表示可以把属于同一个应用层的PDU的TCP包虚拟地集中起来，在最后形成一个完整的包。

需要在Wireshark里面启用这个配置。在：Preferences→ Protocols→TCP菜单，如下图：

![wireshark_pcapng32](/images/wireshark_pcapng32.png)

## Continuations to

跟上面的相反，需要关闭Allow sub dissector to reassemble tcp streams这个开关，就会显示这个信息。

## Time-to-live exceeded（Fragment reassembly time exceeded)

表示这个包的发送方之前就收到了一些分片，但是由于某些原因迟迟无法组装起来。

# 问题

## 为什么MacBook上提示：You don’t have permission to capture on local interfaces.

解决方法：

```bash
zhengxianle@zhengxianledeMacBook-Pro ~> whoami
zhengxianle
zhengxianle@zhengxianledeMacBook-Pro ~> cd /dev
zhengxianle@zhengxianledeMacBook-Pro /dev> ls -l /dev/bp*
crwx--x--x  1 root  wheel  0x17000000  5 23 08:53 /dev/bpf0*
crwx--x--x  1 root  wheel  0x17000001  5 23 08:53 /dev/bpf1*
crwx--x--x  1 root  wheel  0x17000002  5 17 11:42 /dev/bpf2*
crwx--x--x  1 root  wheel  0x17000003  5 17 11:42 /dev/bpf3*
zhengxianle@zhengxianledeMacBook-Pro /dev> sudo chown zhengxianle:admin bp*
zhengxianle@zhengxianledeMacBook-Pro /dev> ls -l /dev/bp*
crwx--x--x  1 zhengxianle  admin  0x17000000  5 23 08:53 /dev/bpf0*
crwx--x--x  1 zhengxianle  admin  0x17000001  5 23 08:53 /dev/bpf1*
crwx--x--x  1 zhengxianle  admin  0x17000002  5 17 11:42 /dev/bpf2*
crwx--x--x  1 zhengxianle  admin  0x17000003  5 17 11:42 /dev/bpf3*
```

参考：[https://andrewbaker.ninja/2023/01/14/macbook-fixing-the-wireshark-permissions-bug-you-dont-have-permission-to-capture-on-that-device/](https://andrewbaker.ninja/2023/01/14/macbook-fixing-the-wireshark-permissions-bug-you-dont-have-permission-to-capture-on-that-device/)

## 四次挥手的疑惑

如下图，这个包的ack为什么没有加1呢？

![wireshark_pcapng33](/images/wireshark_pcapng33.png)

其实是因为延迟确认导致的这个问题，因为它省掉了四次挥手中的第二个包，所以流程如下图：

![wireshark_pcapng34](/images/wireshark_pcapng34.png)

因为启用了延迟确认，所以在第二个包和第三个包进行了合并。

但是图中的第319个包的ACK逻辑如下：

![wireshark_pcapng35](/images/wireshark_pcapng35.png)

此时，319相当于317的ack，但是318已经发出去了，所以319根本不算挥手的过程包。

这里只有318、320、321是真正的挥手包。