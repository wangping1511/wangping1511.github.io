---
layout: article
title: 安装 RabbitMQ
tags: 运维 RabbitMQ
---



## 安装

### Linux
#### 下载安装

- **下载、安装 Erlang** [下载源](https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.8-1.el7.x86_64.rpm)
```bash
wget --content-disposition https://packagecloud.io/rabbitmq/erlang/packages/el/7/erlang-21.3.8.8-1.el7.x86_64.rpm/download.rpm
yum localinstall erlang-21.3.8.8-1.el7.x86_64.rpm
```
查看 Erlang 版本
```bash
erl -verion
```

- **安装 RabbitMQ**
```bash
wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.8.0/rabbitmq-server-3.8.0-1.el7.noarch.rpm
yum localinstall rabbitmq-server-3.8.0-1.el7.noarch.rpm
```


#### 使用、配置

- **启动、停止**
```bash
systemctl start rabbitmq-server
systemctl enable rabbitmq-server
systemctl status rabbitmq-server
```

- **创建管理用户**

默认 guest 用户只允许 localhost 访问
```bash
# 创建用户
rabbitmqctl add_user username password
# 授权
rabbitmqctl set_user_tags username [administrator]
rabbitmqctl set_permissions -p / username ".*" ".*" ".*"
```

- **启用管理界面**
```bash
rabbitmq-plugins enable rabbitmq_management
```
访问地址: `http://IP:15672/`

- **设置防火墙**
```bash
firewall-cmd --zone=public --permanent --add-port=4369/tcp
firewall-cmd --zone=public --permanent --add-port=25672/tcp
firewall-cmd --zone=public --permanent --add-port=5671-5672/tcp
firewall-cmd --zone=public --permanent --add-port=15672/tcp
firewall-cmd --zone=public --permanent --add-port=61613-61614/tcp
firewall-cmd --zone=public --permanent --add-port=1883/tcp
firewall-cmd --zone=public --permanent --add-port=8883/tcp
```
刷新防火墙配置：
```bash
firewall-cmd --reload
```



## Springboot 集成

