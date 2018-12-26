---
title: 第一次使用Zabbix
date: 2018-11-29 22:56:21
tags:
    - Zabbix
    - Docker
---

> 公司做是教育的，有很多服务器跑在校园内。因为程序不够健壮，导致会有很多小问题，随着公司业务扩大，服务器越来越多。为了能够及时发现、处理这些问题我们需要一款监控软件来帮助我们监控这些应用。我们最终选定了 **Zabbix** 作为我们的监控报警软件。

#### 部署Zabbix服务端

官网看到当前`Zabbix`版本是`4.0`，服务端我们选择在`docker`中部署`Zabbix appliance`。该镜像内置了`MySQL`、`Zabbix Server`、`Nginx`，安装命令也较为简单。

```shell
docker run --name zabbix-appliance -t \
    -p 10051:10051 \
    -p 80:80 \
    -d zabbix/zabbix-appliance:latest
```

自此，服务端安装完成。<font color=#b1b1b1>真是简单到猴子都能看懂了</font>

这个时候你就可以去登录`Zabbix`服务端了，默认用户名为`Admin`密码`zabbix`。

#### 部署Zabbix-agent

由于我们要监控网卡流量、系统内的进程等，所以`zabbix-agent`我们直接安装在被监控的机器上，而不是采用`docker`安装。

在`Ubuntu`上安装：

```shell
apt install zabbix-agent
```

修改`zabbix-agent`配置文件`vim /etc/zabbix/zabbix_agentd.conf`，设置监控端的`IP`地址和本机主机名。<p><font color=#FF4040>**注：**本地主机名必须配置的与服务端一致。</font></p>
