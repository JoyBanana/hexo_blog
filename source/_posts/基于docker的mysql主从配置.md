---
title: 基于Docker的MySQL主从配置
date: 2019-2-20 22:12:11
tags:
    - Docker
    - MySQL
---
这个很简单，直接上操作.
### 创建数据库
```bash
#主库
docker run --name mysql-master \
-p 6012:3306 \
-v /opt/data/mysql-master/data/:/var/lib/mysql \    #数据持久化
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

```bash
#从库
docker run --name mysql-slave \
-p 6014:3306 \
-v /opt/data/mysql-slave/data/:/var/lib/mysql \    #数据持久化
-v /etc/localtime:/etc/localtime \
-e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7
```

### 配置MySQL的配置文件

这个文件配置内容有时间再好好搜一下啥意思，回头更新。

```conf

[mysqld]
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
datadir		= /var/lib/mysql
#log-error	= /var/log/mysql/error.log
# By default we only accept connections from localhost
#bind-address	= 127.0.0.1
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

# 设置server-id
server-id=1

#binlog
# 开启二进制日志
log-bin=mysql-bin
# 以行的方式
binlog_format=row
#binlog-do-db=
#binlog-ignore-db=mysql
skip_slave_start=1

#GTID
gtid_mode=ON
enforce_gtid_consistency=true
init_connect='SET collation_connection = utf8_unicode_ci'
init_connect='SET NAMES utf8'
character-set-server=utf8
collation-server=utf8_unicode_ci
sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION'
lower_case_table_names=1
max_allowed_packet=500M
```

**从库的配置文件的 `server-id` 不和主库一样就行。**


创建完后我们分别把这两个文件拷到两个 `MySQL`  容器内。

```bash
#主库
docker cp my.cnf mysql-master:/etc/mysql/
#从库
docker cp my.cnf mysql-slave:/etc/mysql/
#大概是这个意思，将就看
#考完以后重启下这俩容器
docker restart mysql-master
docker restart mysql-slave
```

现在我们可以用 `Workbench` 连接下俩数据库。


连上后在主库里我们跑一些这个命令看下二进制文件名和 `position` 的值。这个等下要用。

```
show master status;
```

然后去从库里，配置主库信息。
```
change master to master_host='172.18.0.2', master_user='root', master_password='123456', master_port=3306, master_log_file='mysql-bin.000002', master_log_pos= 154, master_connect_retry=30;test2table
```
这个 `master_log_file` 就是我们查的主库文件名，`master_log_pos` 就是 `position` 的值。


最后在从库里 `start slave` 开启同步。这样就OJBK了。可以主库创建个表啊之类的看看从库有没有更新。
