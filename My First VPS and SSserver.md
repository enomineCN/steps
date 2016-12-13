---
title: My First VPS and SSserver
date: 2016-11-26 12:20:33
tags: 
- VPS
- Shadowsocks
- SSserver
---

## 前言
早在2011年，我的高中同学JQ.Lee就给我普及了天朝GFW的相关知识，以及教会我科学上网的正确姿势，从此，一扇新的大门向我敞开。
当时，爱折腾的JQ.Lee就自己到国外买了台服务器，搭建起了自己的SSserver，他也十分大方的将自己的SSserver与我共享。
就这样我享受着自由上网，直到后来，他的梯子越来越不稳定，我就想自己搭梯子了。
## VPS选择
在网上搜索VPS的时候，看到[老左博客](http://www.laozuo.org/)有各种VPS的介绍和评测。推荐的有[BandwagonHost](https://bwh1.net/index.php)、[Vultr](https://www.vultr.com/)、[Linode](https://www.linode.com/)三个性价比比较高的VPS，搬瓦工最便宜但是用的人太多，最近特别不稳定，最关键是官网不能直接访问，如果梯子挂了还要找别的梯子，于是就在[Vultr](https://www.vultr.com/)和[Linode](https://www.linode.com/)里面选了一个较为便宜的Vultr，月付5刀。

## Shadowsocks介绍

## Shadowsocks Server 搭建

首先SSH连接上VPS服务器。一般Linux系统都自带Python环境。

### 1.安装Shadowsocks
CentOS:
```bash
yum install python-setuptools && easy_install pip  
pip install shadowsocks  
```
### 2.优化Shadowsocks性能
按照 SS 官方 Wiki，有如下优化策略：
创建` local.conf `配置文件：`vim /etc/sysctl.d/local.conf`
```bash
# max open files
fs.file-max = 51200  
# max read buffer
net.core.rmem_max = 67108864  
# max write buffer
net.core.wmem_max = 67108864  
# default read buffer
net.core.rmem_default = 65536  
# default write buffer
net.core.wmem_default = 65536  
# max processor input queue
net.core.netdev_max_backlog = 4096  
# max backlog
net.core.somaxconn = 4096

# resist SYN flood attacks
net.ipv4.tcp_syncookies = 1  
# reuse timewait sockets when safe
net.ipv4.tcp_tw_reuse = 1  
# turn off fast timewait sockets recycling
net.ipv4.tcp_tw_recycle = 0  
# short FIN timeout
net.ipv4.tcp_fin_timeout = 30  
# short keepalive time
net.ipv4.tcp_keepalive_time = 1200  
# outbound port range
net.ipv4.ip_local_port_range = 10000 65000  
# max SYN backlog
net.ipv4.tcp_max_syn_backlog = 4096  
# max timewait sockets held by system simultaneously
net.ipv4.tcp_max_tw_buckets = 5000  
# turn on TCP Fast Open on both client and server side
net.ipv4.tcp_fastopen = 3  
# TCP receive buffer
net.ipv4.tcp_rmem = 4096 87380 67108864  
# TCP write buffer
net.ipv4.tcp_wmem = 4096 65536 67108864  
# turn on path MTU discovery
net.ipv4.tcp_mtu_probing = 1

# for high-latency network
net.ipv4.tcp_congestion_control = hybla

# for low-latency network, use cubic instead
# net.ipv4.tcp_congestion_control = cubic
```
### 3.配置 Shadowsocks 配置文件
在 root 文件夹内，新建` ss/ssserver.json` 配置文件：`vim ~/ss/ssserver.json`
```json
{
    "server": "server_ip",
    "server_port": 1088,
    "local_address": "127.0.0.1",
    "local_port": 1080,
    "password": "yourpassword",
    "timeout": 300,
    "method": "aes-256-cfb",
    "fast_open": false
}
```
### 4.启动并永久运行 Shadowsocks 服务端功能

- 启动命令
  `nohup ssserver -c /root/ss/ssserver.json -d start &`
> 说明：`ssserver` 是 SS 的服务端命令。`-c` 表示以配置文件的方式运行 SS，`/root/ss/ssserver.json` 则是步骤 4 中新建的配置文件的路径。`-d` 表示在后台运行，这样允许用户进行其他操作。`start` 就是启动。`nohup` 以及最后的 `&` 是让 SS 服务端一直运行，并把运行日志输出到当前用户主目录下的 nohup.out 文件中。

- 停止命令
  `ssserver -c /root/ss/ssserver.json -d stop`

### 5.防火墙打开服务端口
```bash
firewall-cmd --permanent --zone=public --add-port=1088/tcp
firewall-cmd --permanent --zone=public --add-port=1088/udp
firewall-cmd --reload
```

## 配置SS客户端

- MAC
- [Windows](https://github.com/shadowsocks/shadowsocks-windows)
- IOS(AppStore: Wingy)
- [Android](https://github.com/shadowsocks/shadowsocks-android)


