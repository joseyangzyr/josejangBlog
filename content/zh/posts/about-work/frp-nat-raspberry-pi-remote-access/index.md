---
title: "frp 内网穿透，远程连接树莓派"
date: 2021-07-10T20:08:00+08:00
slug: "frp-nat-raspberry-pi-remote-access"
description: "记录通过 frp 实现内网穿透，从外部网络远程连接树莓派的实际操作过程，包括热点组网、IP 探测和基础 frp 配置。"
summary: "记录通过 frp 实现内网穿透，从外部网络远程连接树莓派的实际操作过程，包括热点组网、IP 探测和基础 frp 配置。"
categories:
  - 树莓派
tags:
  - frp
  - 内网穿透
  - 树莓派
  - SSH
  - 远程连接
aliases:
  - /raspberry-pi/190.html
---

昨天刚拿到的树莓派，今天开始折腾
环境iphone12（热点）安卓机Termux命令行用来连接iPhone的热点，树莓派连接iphone12开的热点
Termux：https://play.google.com/store/apps/details?id=com.termux&hl=zh&gl=US
这三个形成是同一局域网： iphone12，安卓，树莓派
确定iphone手机上显示为2个连接
先用安卓机：
然后尝试ifconfig 得到安卓机本地ip 172.20.10.3

> nmap -SP 172.20.10.0/24

得到树莓派本地ip 172.20.10.9
然后就可以用安卓机ssh连接啦

关于更多内网穿透的内容请访问：
推荐5款最好用的内网穿透工具：https://www.josejang.com/copy/181.html

ok 开始配置frp：

## frp服务端

在你要部署frp的服务器或vps上运行

> getconf LONG_BIT

得到结果：

> 64

再查看下当前服务器版本架构

> arch

> https://github.com/fatedier/frp/releases

下载软件包时选择对应的版本：

386	X86架构
386	X86架构
amd64	amd架构
arm	arm架构
arm64	64位arm架构
mips	mips架构
mips64	64位mips架构
mipsle	mipsle架构
mips64le	64位mipsle架构

我选择的是：
frp_0.37.0_linux_arm64.tar.gz

> wget https://github.com/fatedier/frp/releases/download/v0.37.0/frp_0.37.0_linux_arm64.tar.gz

然后解压：

> tar xvf frp_0.37.0_linux_arm64.tar.gz

移动：

> mv frp_0.37.0_linux_arm64 /usr/local/frp

配置文件：

> nano /usr/local/frp/frps.ini

在打开的文件中写入：

> [common]
> bind_port = 6000
> dashboard_port = 6500
> dashboard_user = 你的仪表盘用户名
> dashboard_pwd = 你的仪表盘密码

ctrl+x 出现提示按Y

启动服务：

> sudo nohup /usr/local/frp/frps -c /usr/local/frp/frps.ini>/usr/local/frp/frps.log 2>&1 &

到这里服务端配置结束（先不要关闭你的命令行窗口，等下还有设置开启自启）

## frp客户端

同服务端，先查看系统是32位还是64位，我今天刚在树莓派官网下载的32位系统，所以这里略过了，命令同上
这次需要下载frp_0.37.0_linux_arm.tar.gz
安卓机ssh链接到树莓派后输入：

> wget https://github.com/fatedier/frp/releases/download/v0.37.0/frp_0.37.0_linux_arm.tar.gz

步骤同服端，解压

> tar xvf frp_0.37.0_linux_arm.tar.gz

移动：

> mv frp_0.37.0_linux_arm /usr/local/frp

配置文件：(注意这里的是frpc，服务端是frps)

> nano /usr/local/frp/frpc.ini

在打开的文件中写入：

> [common]
> server_addr = 你的服务器的IP地址
> server_port = 6000
> [ssh] # 任意设置，需要保证注册到服务端是唯一端
> type = tcp # 穿透tcp类型的流量
> local_ip = 127.0.0.1  # 局域网地址，可以是非本机ip
> local_port = 22  # 自己电脑上端服务端口
> remote_port = 7000 # 这个端口会在服务器端监听，后续公网访问通过该端口

由于我首次安装，不知道能否链接上，所以先就填上一个端口吧

启动服务：

> sudo /usr/local/frp/frpc -c /usr/local/frp/frpc.ini

![](F88DA3EE-43C8-47EA-BDF2-103F9D7CD4BE-300x225.jpeg)
到此配置结束，现在你就可以在试试ssh -p 7000 pi@你的服务器的IP地址，看看是否能链接上呢？

## 配置开机启动

服务端配置开机启动：

> sudo nano /etc/systemd/system/frpc.service

在文档中写入如下内容：

> [Unit]
> Description=frpc daemon
> After=multi-user.target
> [Service]
> Restart=always
> RestartSec=5
> TimeoutStartSec=30
> WorkingDirectory=/usr/local/frp
> ExecStart=/usr/local/frp/frpc -c /usr/local/frp/frpc.ini
> Restart=on-failure
> [Install]
> WantedBy=multi-user.target

ctrl+x 出现提示按Y

打开开机自启动

> sudo systemctl enable frps

关闭开机自启

> systemctl disable frps

客户端操作与服务端相同，只是把上面的frps改成frpc

重启

> sudo systemctl restart frps

启动 frps

> sudo systemctl start frps

停止

> sudo systemctl stop frps

查看应用日志

> sudo systemctl status frps

## 更多配置

frps 完整配置文件
https://github.com/fatedier/frp/blob/master/conf/frps_full.ini

frpc 完整配置文件
https://github.com/fatedier/frp/blob/master/conf/frpc_full.ini

