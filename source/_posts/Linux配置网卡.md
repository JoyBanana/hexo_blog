---
title: Linux配置网卡
date: 2018-7-6 10:09:12
tags:
    - 网卡
---
进行操作前请先查一下你的网卡叫什么名字,可以用`ip addr`或者`ifconfig`,会列出系统所有的网卡,第一个一般是localhost(本地环回),它的名字叫`lo`,下面的是其他网卡,有线网卡一般叫`enp3s0`之类的名字。知道了网卡的名字，接下来我们要去配置它。

首先要先要获取root权限,这样我们对配置文件才有写权限。

### Debian系 <font color=#DCDCDC>以UbuntuServer 16.04为例</font>

它的配置文件是`/etc/network/interface` 我们使用 `vim` 来编辑这个文件。

在本地环回下新建网卡配置为 `static` 或者 `dhcp`

如果使用静态IP，我们要配置这几个值：
```bash
auto yourNIC
iface yourNIC inet static
address xxx.xxx.xx.xx
netmask xxx.xxx.xx.xx
gateway xx.xxx.xxx.xx
dns-nameservices x.x.x.x x.x.x.x
```
如果该网卡是无线网卡，需要添加：
```bash
wpa-ssid yourssid
wpa-psk yourpasswd
```
```bash
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
        #or dns-nameservers 8.8.8.8 8.8.4.4
```

示例文件配置 如下图

<div class="666">
<img src="/posts_img/network_interface.png" width="100%" >
</div>

**网卡生效**

在网上查了下大家都说`/etc/init.d/networking restart`可以让网卡重启并生效，我自己试了下好像并不能让新配置的ip生效。<del>需要重启系统才能让设置生效</del>。可以通过启动关闭网卡来设置生效。`ifdown -a && ifup -a`

<div><font color="#00BCD4">tips：</font>在机房给服务器安装操作系统时，通常情况下是需要配置固定IP。安装时我们可以先随便选择一个网卡，在安装好操作系统后再配置网络。使用`ifdown netWorkDevice`、`ifup netWorkDevice`可以关闭、启用单个网卡，在调整网络时，可能会非常有用。</div>

### Redhat系 <font color=#DCDCDC>以CentOS 7.1为例</font>

与`Debian`系不同，需要修改`/etc/sysconfig/network-scripts/`下的文件。这个文件夹下不少文件，需要修改的是`ifcfg-XXX`的文件，这个名字和上面写的差不多。本地环回叫`lo`,有线网卡`ensxx`、`enp0xxx`之类的名字。

<div class="666">
<img src="/posts_img/network_interface2.png" width="100%" >
</div>

修改的值也是和Ubuntu类似。
``` bash
#修改
BOOTPROTO=static #这里讲dhcp换成ststic
ONBOOT=yes #将no换成yes 意为开机开启此网口
#新增
IPADDR=xxx.xx.xxx.x #静态IP
GATEWAY=xxx.xx.xxx.x #默认网关
NETMASK=xxx.xxx.xxx.x #子网掩码
```

DNS的配置可在`/etc/resolv.conf`文件中添加如下配置
```bash
nameserver 114.114.114.114
nameserver 8.8.8.8
```

示例文件配置 如下图

<div class="666">
<img src="/posts_img/network_interface3.png" width="100%" >
</div>

**网卡生效**

使用 `systemctl restart network` 即可生效。<font color=#c0c0c0>*我也不知道为什么Ubuntu这样不行（哭）*<font>

**说句题外话：**

昨天使用虚拟机突然开启不了网卡了，报错：`Failed to start LSB: Bring up/down networking`
万能的Google告诉我禁用NetworkManager就OK了。
```bash
systemctl stop NetworkManager
systemctl disable NetworkManager
```
<div align="right"><font color=#e5e5e5>*更新于2018-10-03 23:31*</font></div>
