---
layout: article
title: 安装 RabbitMQ
tags: 运维 RabbitMQ
---

## 安装

### Linux

Erlang RabbitMQ 版本对应关系：

|                        RabbitMQ 版本                         | Erlang/OTP 版本 |
| :----------------------------------------------------------: | :-------------: |
|                            3.8.16                            |  23.2 -  24.x   |
| 3.8.15 <br />3.8.14 <br />3.8.13 <br />3.8.12 <br />3.8.11<br /> 3.8.10 <br />3.8.9 |   22.3 - 23.x   |

[更多版本对应关系]([RabbitMQ Erlang Version Requirements — RabbitMQ](https://www.rabbitmq.com/which-erlang.html))

#### 下载安装

- **下载、安装 Erlang**  [下载源](https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.8-1.el7.x86_64.rpm)
```bash
$ wget --content-disposition https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-23.3.4-1.el7.x86_64.rpm/download.rpm
$ yum localinstall erlang-23.3.4-1.el7.x86_64.rpm
```
查看 Erlang 版本

```bash
$ erl -verion
```

- **安装 RabbitMQ**
```bash
$ wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.0/rabbitmq-server-3.8.0-1.el7.noarch.rpm
$ yum localinstall rabbitmq-server-3.8.0-1.el7.noarch.rpm
```


#### 使用、配置

- **开机自启、启动、停止**
```bash
$ systemctl enable rabbitmq-server
$ systemctl start rabbitmq-server
$ systemctl status rabbitmq-server
```

- **创建管理用户**

默认 guest 用户只允许 localhost 访问
```bash
# 创建用户
$ rabbitmqctl add_user username password
# 授权
$ rabbitmqctl set_user_tags username [administrator]
$ rabbitmqctl set_permissions -p / username ".*" ".*" ".*"
```

- **启用管理界面**
```bash
$ rabbitmq-plugins enable rabbitmq_management
```
访问地址: `http://IP:15672/`

- **设置防火墙**
```bash
# 服务端口
$ firewall-cmd --zone=public --permanent --add-port=5672/tcp
# 管理台端口
$ firewall-cmd --zone=public --permanent --add-port=15672/tcp
# 刷新防火墙
$ systemctl restart firewalld
```
## Springboot 集成

