---
layout: post
title: Ubuntu18.04 使用 nmap 查找 ip，并配置静态 ip
date: 2023-07-28 09:18 +0800
categories:
- 学习随笔
- Linux
tags:
- Linux
- 静态 ip
- umap
---



## 前言

笔者之前服务器用的是局域网的 ip，这就导致服务器的 ip 会经常发生改变。每次改变后，都是傻乎乎的去服务器上物理查看服务器的 ip 地址。于是有了本篇。面对这种问题，笔者目前找到了两种方法：

- 使用 nmap 查找服务器 ip（不方便）
- `将服务器 ip 配置成静态 ip（一劳永逸）`

   

## 使用 nmap 查找服务器 ip 

- 首先查看局域网的网段是多少。比如我查看我的本机 ip 为：`192.168.0.10`，这通常就说明网段为：`192.168.0.xxx/24`

- 安装 `umap`，安装完毕后，执行：`sudo nmap -p 22 192.168.0.0/24` 查找`该局域网内所有开放了 22 端口的主机 ip`

  > 22 端口为 ssh 的默认端口

- 一个个去尝试连接，直到连接成功



## 将服务器的 ip 设置为静态

在服务器上操作：

- 安装 netplan：`sudo apt install netplan`

- 查看网关：`route -n`，下面输出中 `192.168.0.1` 即为当前服务器的网关

  ```bash
  Kernel IP routing table
  Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
  0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 enp129s0f0
  192.168.0.0     0.0.0.0         255.255.255.0   U     0      0        0 enp129s0f0
  ```

- 查看 ip 和网卡名称：`ifconfig`，下面输出中 `192.168.0.11` 即为当前服务器的 ip 地址，`enp129s0f0` 即为 ip 地址对应的网卡名称

  ```bash
  docker0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
          ether 02:42:e2:96:fa:04  txqueuelen 0  (Ethernet)
          RX packets 0  bytes 0 (0.0 B)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 0  bytes 0 (0.0 B)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  
  enp129s0f0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
          inet 192.168.0.11  netmask 255.255.255.0  broadcast 192.168.0.255
          ether ac:1f:6b:99:01:5a  txqueuelen 1000  (Ethernet)
          RX packets 16657889  bytes 7656373230 (7.6 GB)
          RX errors 0  dropped 0  overruns 0  frame 0
          TX packets 11409396  bytes 11102874210 (11.1 GB)
          TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
          device memory 0xfb320000-fb33ffff
  ```

- 备份 netplan 的 yaml 配置文件：`sudo cp 01-network-manager-all.yaml 01-network-manager-all.yaml.back`

  > yaml 文件名称可能有所不同

- 修改 yaml 配置文件：`sudo vim 01-network-manager-all.yaml`

  > #Let NetworkManager manage all devices on this system
  >
  > network:
  >   version: 2
  >   ethernets:
  >     enp129s0f0:  # 上面获得的网口名称
  >       addresses: [192.168.0.11/24]  # 上面获得的 ip
  >       dhcp4: no
  >       gateway4: 192.168.0.1  # 上面获得的网关
  >       optional: true
  >       nameservers:
  >                addresses: [223.5.5.5, 223.6.6.6]

- 使配置生效：`sudo netplan apply`

- 测试配置结果：`ping www.baidu.com` 成功 ping 通



## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
