---
title: Open vSwitch的简单应用
date: 2019-2-18 17:34:11
tags:
    - Open vSwitch
---

>`docker` 真是无所不能，使一切管理都变得简单清晰起来。但是使用 `docker`也带来一些小了问题。
>生产环境中，使用多台服务器协同生产是很正常的。如果都使用 `docker` 部署如 `MySQL` ，需要做主从同步，但是为了容灾，宿主机又不是同一服务器。就会出现通讯的问题。

#### 使用 Open vSwitch
`Open vSwitch` 是一个虚拟交换机。我们通过她可实现 `docker` 跨主机通信。

**安装**
```shell
#CentOS
yum install openvswitch
#Ubuntu & Debian
apt install openvswitch-switch
#Arch
pacman -S openvswitch
```

**修改docker0的网段**

修改完docker的网段后，新生成的容器会变为配置所在网段。
```bash
vim /lib/systemd/system/docker.service
#主机1
ExecStart=/usr/bin/dockerd --bip=172.17.1.1/24
#主机2
ExecStart=/usr/bin/dockerd --bip=172.17.2.1/24
systemctl daemon-reload
systemctl restart docker
```

**创建网桥、建立gre隧道**

```bash
#创建网桥 br0
ovs-vsctl add-br br0
#激活网桥
ip link set dev br0 up
#设置gre隧道
ovs-vsctl add-port br0 gre0 -- set Interface gre0 type=gre options:remote_ip=192.168.8.113 #这里是你的另一台主机地址

# 如果有多个节点，需要添加多条greX（gre0,gre1,...）

## 将docker0加入br0
brctl addif docker0 br0

###另一个主机做相同操作。gre隧道地址填写为这一台主机
```

**建立静态路由，便可以互相访问**

```sh
ip route add 172.17.0.0/16 dev docker0
#到这里，两个主机内的docker容器便可以互相ping通
```

**开机自启**

```bash
#该方法重启后会失效，可以创建如下脚本，添加到/etc/rc.local，或者crontab @reboot内
# 启动br0网桥
ip link set dev br0 up
# 将docker0添加到br0中
brctl addif docker0 br0
# 添加静态路由
ip route add 172.17.0.0/16 dev docker0
```
