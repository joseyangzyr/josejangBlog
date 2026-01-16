---
title: "Debian 下绑定、添加多 IP 的方法（单网卡）"
date: 2022-01-27
slug: debian-multi-ip-single-nic
description: "记录在 Debian 系统中单网卡绑定多个 IP 的配置方法，通过 interfaces 文件实现多 IP 配置，适用于服务器多出口或多业务场景。"
categories:
  - 工作
summary: "记录在 Debian 系统中单网卡绑定多个 IP 的配置方法，通过 interfaces 文件实现多 IP 配置，适用于服务器多出口或多业务场景。"
aliases:
  - /about-work/286.html
---

```shell
sudo nano /etc/network/interfaces
```

进入文件之后点击i进入编辑模式，仿照如下添加第二个ip（enp6s0是网卡，根据个人系统情况修改），有的系统是allow-hotplug，可以直接修改为auto

```shell
auto enp6s0
iface enp6s0 inet static
        address 192.168.0.1
        netmask 255.255.255.248
        gateway 204.12.220.225
        # dns-* options are implemented by the resolvconf package, if installed
        dns-nameservers 8.8.8.8
        dns-search opthappiness.com
auto enp6s0:1
iface enp6s0:1 inet static
        address 192.168.0.2
        netmask 255.255.255.248
auto enp6s0:2
iface enp6s0:2 inet static
        address 192.168.0.3
        netmask 255.255.255.248
auto enp6s0:3
iface enp6s0:3 inet static
        address 192.168.0.4
        netmask 255.255.255.248
auto enp6s0:4
iface enp6s0:4 inet static
        address 192.168.0.5
        netmask 255.255.255.248
```

注意，新增ip不要写gateway（网关），如果还要继续添加ip，同理再在下面添加enp6s0:5、enp6s0:6….

保存文件，并执行如下命令刷新网络

```shell
# /etc/init.d/networking restart
```

或者：

```shell
$ sudo /etc/init.d/networking restart
```

