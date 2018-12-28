---
title: Docker的基本安装和使用
date: 2018-7-16 16:33:51
tags:
    - Docker
    - Ubuntu
    - MySQL
    -
---


### Docker的基本安装和使用

> **Docker** 现在应用真的是越来越广泛了，越来越多的企业用它来部署项目，真的很方便。
> **Docker** 在`yum`和`apt`两大主流包管理器上，好像都已经有了**Docker**，可以直接安装。

#### 下面以Ubuntu为例

切换到root用户下操作，直接安装。
```shell
apt install docker.io
```

与`apache2`不同，`docker`服务安装完成是默认是**没有启动**的。如果直接使用（比如说`pull`、`ps`等命令），会报：
```
FATA[0000] Post http:///var/run/docker.sock/v1.18/images/create?fromImage=mysql%3Alatest:
dial unix /var/run/docker.sock: no such file or directory. Are you trying to connect to a TLS-enabled daemon without TLS?
```

可以用`service docker status`看下，`docker`的那个小点点应该不是绿色的。记得要先启动`docker`服务`service docker start`然后再操作。


### 使用Docker安装MySQL
> 写MySQL的原因还是涉及到数据持久化的问题，如果不把数据映射到主机硬盘上的话，下次开启就没有数据了。

配置MySQL可以查找官方镜像并拉取
```shell
docker search mysql #在官方的Docker Hub上查找合适的mysql版本，后边还有点赞数
docker pull mysql #我一般选star多的，直接pull下来
```
#### 启动mysql

```
docker run -p 3306:3306 --name mymysql -v /opt/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql
```
我们使用`-v`参数将`mysql`的数据指向宿主机的`/opt/data`文件夹下，这样以后启动的话数据也不会丢失。

#### Docker的一些简单常用命令

| 命令 | 含义 |
| :-: | :-: |
|`docker ps`|查询正在运行的实例|
|`docker start/stop/restart ‘yourInstance’`| 开启、停止、重启你的实例|
|`docker exec -it ‘yourInstance’ bash`| 进入你的实例容器|
|`docker rm ‘yourInstance’` | 移除你的容器|

你可以使用`docker`做很多事情，甚至可以在x86架构的平台上运行arm程序！
