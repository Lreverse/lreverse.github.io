---
title: bettercap的使用
date: 2024-04-11 21:26:47 +0800
categories: [Tools, 渗透]
math: true
---

### 安装

```bash
sudo apt-get install bettercap
```

### 启动

启动bettercap，默认开启`eth0`网卡

```bash
sudo bettercap
```

自行指定网卡进行启动

```bash
sudo bettercap -iface wlan0
```


### 使用

bettercap是有很多模块的，不同模块有不同的功能，要想使用某一模块，就需要先启动它。

#### **net.recon**
该模块会定期读取 ARP 缓存，以监控网络上的新主机。开启它来发现该子网内存活的主机。

```bash
net.recon on
```

然后列出局域网内存活的主机

```bash
net.show
```

#### **net.probe**

该模块通过向子网中每个可能的IP发送虚假的UDP数据包，不断探测网络中的新主机


#### **arp.spoof**
该模块会不断地欺骗网络上选定的主机。
- arp.spoof.fullduplex：目标和网关都将受到攻击，否则只攻击目标
```bash
set apr.spoof.fullduplex true
```
- arp.spoof.targets：
```bash
set apr.spoof.targets 目标p
```
然后，启动该模块便可以进行arp欺骗

#### **http.proxy**
该模块会将所有的http流量重定位到该模块，允许插入脚本或sslstrip，然后再返回给受害者。
- http.proxy.injectjs：将 URL、路径或 javascript 代码注入每个 HTML 页面
```bash
set http.proxy.injectjs 'alert("Hello world")'
```
- http.proxy.script：js脚本路径
```bash
set http.proxy.script /路径
```
- http.proxy.sslstrip：启用或禁用 SSL stripping
	- SSLstrip 也叫 https 降级攻击，攻击者拦截用户流量后，欺骗用户与攻击者进行 http 通信，攻击者与服务器保持正常通信 （http 或 https），从而获取用户信息
```bash
set http.proxy.sslstrip true
```

#### **dns.spoof**
该模块将以欺骗的方式回复dns查询，以进行dns欺骗
- dns.spoof.domains：要进行欺骗的域名，以逗号进行分隔
```bash
set dns.spoof.domains www.example.com
```
- dns.spoof.address：域名将要映射到的IP地址
```bash
set dns.spoof.address 1.2.3.4
```
- dns.spoof.hosts：使用主机文件进行域名和IP映射
