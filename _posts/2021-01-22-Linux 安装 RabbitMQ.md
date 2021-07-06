---
layout: article
title: 安装 RabbitMQ
tags: 运维 RabbitMQ
---

## 安装

Erlang RabbitMQ 版本对应关系：

|                        RabbitMQ 版本                         | Erlang/OTP 版本 |
| :----------------------------------------------------------: | :-------------: |
|                            3.8.16                            |  23.2 -  24.x   |
| 3.8.15 <br />3.8.14 <br />3.8.13 <br />3.8.12 <br />3.8.11<br /> 3.8.10 <br />3.8.9 |   22.3 - 23.x   |

[更多版本对应关系]([RabbitMQ Erlang Version Requirements — RabbitMQ](https://www.rabbitmq.com/which-erlang.html))

### Linux

#### 下载安装

##### 下载、安装 Erlang  [下载源](https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.8-1.el7.x86_64.rpm)

```bash
$ wget --content-disposition https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-23.3.4-1.el7.x86_64.rpm/download.rpm
$ yum -y localinstall erlang-23.3.4-1.el7.x86_64.rpm
# 查看 Erlang 版本
$ erl -verion
```
##### 安装 RabbitMQ

```bash
$ wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.0/rabbitmq-server-3.8.0-1.el7.noarch.rpm
$ yum -y localinstall rabbitmq-server-3.8.0-1.el7.noarch.rpm
```


#### 使用、配置

##### 开机自启、启动、停止、查看状态

```bash
$ systemctl enable rabbitmq-server
$ systemctl start rabbitmq-server
$ systemctl stop rabbitmq-server
$ systemctl status rabbitmq-server
```

##### 创建管理用户 默认 guest 用户只允许 localhost 访问

```bash
# 创建用户
$ rabbitmqctl add_user username password
# 授权
$ rabbitmqctl set_user_tags username administrator
$ rabbitmqctl set_permissions -p / username ".*" ".*" ".*"
```

##### 启用管理界面

```bash
$ rabbitmq-plugins enable rabbitmq_management
```
访问地址: `http://IP:15672/`

##### 设置防火墙

```bash
# 服务端口
$ firewall-cmd --zone=public --permanent --add-port=5672/tcp
# 管理台端口
$ firewall-cmd --zone=public --permanent --add-port=15672/tcp
# 刷新防火墙
$ systemctl restart firewalld
```
## 卸载

### 卸载 erlang

查看 erlang 安装的相关列表

```bash
$ yum list | grep erlang
```

卸载 erlang 已安装的相关内容

```bash
$ yum -y remove erlang-*
$ yum remove erlang.x86_64
```

删除相关文件

```bash
$ rm -rf /usr/lib64/erlang 
$ rm -rf /usr/local/erlang
```

### 卸载 RabbitMQ

先关闭 rabbitmq 服务

```
$ systemctl stop rabbitmq-server
```

查看 rabbitmq 安装的相关列表

```bash
$ yum list | grep rabbitmq
```

卸载rabbitmq 已安装的相关内容

```bash
$ yum -y remove rabbitmq-server.noarch
```

删除相关数据

```bash
$ rm -rf /var/lib/rabbitmq
$ rm -rf /usr/local/rabbitmq
```

