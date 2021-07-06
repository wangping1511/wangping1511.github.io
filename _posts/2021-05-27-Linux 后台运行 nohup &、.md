---
layout: article
title: Linux 后台运行程序 nohup &、screen、
tags: ["Linux"]
---

## nohup &

`&`  加在一个命令的最后，可以把这个命令放到后台执行

`nohup`  英文全称 no hang up（不挂起），用于在系统后台不挂断地运行命令，退出终端不会影响程序的运行

`output 2>&1 &` 把标准错误（2）重定向到标准输出中（1），而标准输出又导入文件output里面

```
$ nohup java -jar xxx.jar & output 2>&1 &
```

## Screen

> **GNU Screen**是一款由GNU计划开发的用于命令行终端切换的自由软件。用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。GNU Screen可以看作是窗口管理器的命令行界面版本。它提供了统一的管理多个会话的界面和相应的功能。

下载安装 screen

```bash
$ yum install screen
```

screen 常用命令

```bash
## 新建一个 screen session
$ screen -S yourname

## 列出所有的 screen session
$ screen -ls

## 分离某个 session
$ screen -d yourname

## 结束当前 session 并回到 yourname 这个 session
$ screen -d -r yourname
```

screen session 里的所有命令都以 `Ctlr+a` 开始

```
##  在最近使用的两个 window 间切换
Ctlr+a Ctlr+a

## detach 当前 session，但是 screen session 运行的 process 仍在继续运行
Ctlr+a d

## 显示所有的 screen
Ctlr+a w

## 关闭当前的 screen
Ctlr+a k
```

