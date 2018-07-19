---
title: Ubuntu下配置网卡
date: 2018-7-6 10:09:12
tags:
    - 网卡
    - Ubuntu
    -
---
### 我们以Ubuntu为例
首先查一下你的网卡叫什么名字,可以用`ip addr`或者`ifconfig`,会列出系统所有的网卡,第一个一般是localhost(本地环回),它的名字叫`lo`,下面的是其他网卡,有线网卡一般叫`enp3s0`之类的名字。

知道了网卡的名字，接下来我们要去配置它

首先要先要获取root权限,`sudo su`然后输入你的密码,这样我们对配置文件才有写权限。

使用`vim /etc/network/interface`来编辑这个文件

在本地环回下新建网卡配置为`static`或者`dhcp`

如果手动指定ip需要配置的有
```shell
auto yourNIC
iface yourNIC inet static
address xxx.xxx.xx.xx
netmask xxx.xxx.xx.xx
gateway xx.xxx.xxx.xx
dns-nameservices x.x.x.x x.x.x.x
```
如果是无线网卡需要在下面添加
```
wpa-ssid yourssid
wpa-psk yourpasswd
```
```shell
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto enp3s0
iface enp3s0 inet static
        address 192.168.8.250
        netmask 255.255.255.0
        gateway 192.168.8.1
        dns-nameserver 192.168.8.1

```

<div class="666">
<img src="/posts_img/network_interface.png" width="80%" >
</div>

配置完成后保存退出就可以了,在网上查了下大家都说`/etc/init.d/networking restart`可以让网卡重启并生效，我自己试了下好像并不能让新配置的ip生效。

需要`reboot`重启系统才能让设置生效.
