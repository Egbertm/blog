---
layout: post
title:  "服务器虚拟化技术（二）云环境的网络连接（NAT,bridge）"
date:   2017-1-21 23:06:05
categories: 服务器
tags:  服务器 虚拟化
---
* content  
{:toc}  

### 虚拟机的两种网络连接介绍。  
**NAT模式** NAT,网络地址转换。它的作用就是能够让虚拟机借助宿主主机所在的网络环境来访问公网。在这种模式下虚拟机可以理解成没有自己的独立网卡。所有访问虚拟机的请求其实是直接发送给宿主机，然后通过访问宿主机转发到虚拟机上的。




相应的虚拟机访问其他网络，也是先转发到宿主机然后在转发出去。对于宿主机之外的网络，是不知道该虚拟机存在的。NAT模式下虚拟机的TCP/IP配置信息是由VMnet8虚拟网络的DHCP服务提供商提供的。  
![http://o886hn2n8.bkt.clouddn.com//KVM/nat%E6%A8%A1%E5%BC%8F.png](http://o886hn2n8.bkt.clouddn.com//KVM/nat%E6%A8%A1%E5%BC%8F.png)  

**bridge模式** 它是虚拟机拥有自己的独立网卡和IP，然后通过借用宿主机的网卡对外连接网络。它把宿主机的网卡当作了一种桥，通过这个桥连接外网的世界。在这种模式下，可以简单的理解成虚拟机和宿主机是两个不同的机器，有独立IP可以相互访问，就像连接在同一个Hub上的两台电脑。想让它们相互通讯，你就需要为虚拟系统配置IP地址和子网掩码，否则就无法通信（参考dhcp服务器是否开启，如果开启，则可以选择dhcp方式自动获取网络地址）。这种方式最简单，直接将虚拟网卡桥接到一个物理网卡上面，和linux下一个网卡绑定两个不同地址类似，实际上是将网卡设置为混杂模式，从而达到侦听多个IP的能力  
![http://o886hn2n8.bkt.clouddn.com//KVM/bridge%E6%A8%A1%E5%BC%8F.png](http://o886hn2n8.bkt.clouddn.com//KVM/bridge%E6%A8%A1%E5%BC%8F.png)  
NAT模式和Bridge模式的比较：  
NAT模式相当于一个路由器，把所有的虚拟机隔开在一个新的网段中，而这使得各虚拟机能够互通，但是虚拟机和宿主机网络中的其它主机不再同一个网络中因而不能互通。  
Bridge模式，相当于一个交换机相对于宿主机，它和宿主机是同等地位的，对外来用户根本不清楚是宿主机还是虚拟机，能够为局域网用户中配置一台独立的虚拟主机，在灵活性上比较方便。配置也就稍微复杂一点。这里主要介绍bridge的配置静态IP地址的方法。在Ubuntu如下：  
**第一步**，停止网络服务。/etc/init.d/networking stop  

**第二步**修改网络配置文件，vim /etc/network/interfaces  

**第三步**将以下内容加入文件。   

    
    auto lo
    iface lo inet loopback
    auto eth0
    iface eth0 inet manual
    auto br0
    iface br0 inet static
    address 192.168.1.110
    network 192.168.1.0
    netmask 255.255.255.0
    broadcast 192.168.1.255
    gateway 192.168.200.1
    dns-nameservers 8.8.8.8
    bridge_ports eth0
    bridge_stp off
    bridge_fd 0
    bridge_maxwait 0
    
静态配置结束，注意dns的配置，否则可能无法上网。  

**第四步** 如果想自动从DHCP获取，请将一下代码复制到编辑的文件中  
    
    auto lo
    iface lo inet loopback
    auto eth0
    iface eth0 inet manual
    auto br0
    iface br0 inet dhcp
    bridge_ports eth0
    bridge_stp off
    bridge_fd 0
    
配置完成。  

**第五步**重启网络服务 sudo /etc/init.d/networking restart  

  
### 参考文献  
[http://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html](http://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html "http://www.cnblogs.com/ggjucheng/archive/2012/08/19/2646007.html")  
[http://www.cnblogs.com/rainman/archive/2013/05/06/3063925.html](http://www.cnblogs.com/rainman/archive/2013/05/06/3063925.html "http://www.cnblogs.com/rainman/archive/2013/05/06/3063925.html")