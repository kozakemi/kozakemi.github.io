---
title: Linux 网络基础与网络安全
published: 2023-09-04 23:05:07
tags: [Linux安全,网络基础知识,防火墙]
category: Linux操作系统
draft: false
---
### 网络基础

[参考链接](https://www.runoob.com/w3cnote/summary-of-network.html#_label3)

#### ip 

ipv4

ipv6 未普及的

#### 子网掩码 

A B C类网络

/24 =255.255.255.0=8x3

#### 网关

路由器/交换机地址，dhcp分配ip

### 连通性测试 ping

#### 查看ip 两命令

#### ifconfig

```shell
..[kozakemi@arco-x64-kemi] - [~] - [三 8月 30, 08:59]
..[$] <()> ifconfig 
enp5s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.132.22.106  netmask 255.255.255.0  broadcast 10.132.22.255
        inet6 fe80::df66:28:d9d3:8b42  prefixlen 64  scopeid 0x20<link>
        ether c8:7f:54:03:a4:a0  txqueuelen 1000  (Ethernet)
        RX packets 585126  bytes 740451285 (706.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 446947  bytes 45027152 (42.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
        device memory 0x85b00000-85bfffff  
```

#### ip addr

```shell
..[kozakemi@arco-x64-kemi] - [~] - [三 8月 30, 08:59]
..[$] <()> ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: enp5s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether c8:7f:54:03:a4:a0 brd ff:ff:ff:ff:ff:ff
    inet 10.132.22.106/24 brd 10.132.22.255 scope global dynamic noprefixroute enp5s0
       valid_lft 79580sec preferred_lft 79580sec
    inet6 fe80::df66:28:d9d3:8b42/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

为[ip](https://www.runoob.com/linux/linux-comm-ip.html)命令的一种应用

### 网络安全

#### 防火墙

配置

iptables 早期

firewalld 现阶段

两者的关系

iptables在内核之上，firewalld 在iptables之上

firewalld 

##### [ufw](https://www.myfreax.com/how-to-setup-a-firewall-with-ufw-on-ubuntu-22-04/#:~:text=%E5%A6%82%E4%BD%95%E5%9C%A8Ubuntu%2022.04%E9%85%8D%E7%BD%AE%E9%98%B2%E7%81%AB%E5%A2%99ufw%201%20%E5%BA%94%E7%94%A8%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%20%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E6%98%AFINI%E6%A0%BC%E5%BC%8F%E7%9A%84%E6%96%87%E6%9C%AC%E6%96%87%E4%BB%B6%EF%BC%8C%E6%8F%8F%E8%BF%B0%E4%BA%86%E6%9C%8D%E5%8A%A1%E7%9A%84%E9%98%B2%E7%81%AB%E5%A2%99%E8%A7%84%E5%88%99%E3%80%82%20...%202%20%E5%90%AF%E7%94%A8UFW,sudo%20ufw%20disable%20%E7%A6%81%E7%94%A8%E9%98%B2%E7%81%AB%E5%A2%99%E3%80%82%20...%208%20IP%E4%BC%AA%E8%A3%85%20)

##### firewalld

```shell
sudo apt install firewalld
```

检查运行状态

```shell
sudo systemctl status firewalld.service
```

检查运行区域

```shell
sudo firewall-cmd --get-default-zone
```

查看默认区域的规则

```shell
sudo firewall-cmd --zone=public --list-all
```

放行80端口

```shell
sudo firewall-cmd --zone=public --add-port=80/tcp --permanent
```

删除放行80端口

```shell
sudo firewall-cmd --zone=public --remove-port=80/tcp --permanent
```

ban ip

```shell
sudo firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.132.22.231" port protocol="tcp" port="80" reject"
```

```shell
sudo firewall-cmd --permanent --add-rich-rule="rule family="ipv4" source address="10.132.22.106" drop"
```

unban ip

```shell
sudo firewall-cmd --permanent --remove-rich-rule='rule family="ipv4" source address="10.132.22.106" reject'
```

直接删除之前的规则

reject拒绝 drop丢弃

重新加载

```shell
sudo firewall-cmd --reload
```