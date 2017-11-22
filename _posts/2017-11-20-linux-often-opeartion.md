---
layout: post
title:  "Linux 常用配置（1）--网络地址配置"
categories: Linux
tags: Linux
author: SurprisedCat
---

* content
{:toc}

在使用cmd操作linux系统时，常常需要配置网络，网络的配置一般在这几个地方。

## /etc/network/interfaces ##

```shell
auto eth0
iface eth0 inet static
    address 192.168.1.3
    netmask 255.255.255.0
    gateway 192.168.1.1
#   子网信息，可选
    network 192.168.1.0
    broadcast 192.168.1.255
# 以下是添加路由的操作，可选
    up route add -net 192.168.1.0 netmask 255.255.255.0 gw 192.168.1.1
    up route add default gw 192.168.1.1
    down route del default gw 192.168.1.1
    down route del -net 192.168.1.1 netmask 255.255.255.0 gw 192.168.1.1
```

或者配置自动获取ip地址

```shell
auto eth0
iface eth0 inet dhcp
```

excerpt_separator_here
修改完成之后，可以采用重启网络的方式获取新的IP。

## Network-Manager ##

Network-Manager是Debian提供的网络管理工具，方便用户使用GUI的时候配置网络。这个在GUI里面比较常见，就是下图这个配置，不在赘述。

![Network-Manager 网络配置](/assets/debian-network-manager.png)

## Network-Manager和interfaces文件的冲突 ##

**本节援引自：[https://www.cnblogs.com/lcword/p/5917348.html](https://www.cnblogs.com/lcword/p/5917348.html) 作者：lclc**

network-manager和/etc/network/interfaces冲突
如果修改了interfaces ，又配置了network-manager（简称nm），你就会发现出现了一些莫名其妙的问题：

1. interfaces 和 nm 中的网络设置不一样，系统实际的IP是哪个？
2. 有时候莫名其妙的，界面右上角的网络连接图标就丢失了。
3. 明明在nm中配置了正确的网络设置，为什么就上不了网呢？

其实，我们要知道 interfaces 和 nm 之间的关系，这些问题就不难解释了。

network-manager和/etc/network/interfaces的关系

1. 当系统内无第三方网络管理工具（如 nm）时，系统默认使用 interfaces 文件内的参数进行网络配置。
2. 当系统内安装了 nm 之后，nm 默认接管了系统的网络配置，使用 nm 自己的网络配置参数来进行配置。
3. 但若用户在安装 nm 之后（Desktop版本默认安装了nm），自己又手动修改了 interfaces 文件，那 nm 就自动停止对系统网络的管理，系统改使用 interfaces 文件内的参数进行网络配置。此时，再去修改 nm 内的参数，不影响系统实际的网络配置。若要让 nm 内的配置生效，必须重新启用nm 接管系统的网络配置。

现在知道了两者之间的工作关系，再看上面的三个问题：

1. 要看nm是否接管，如果没有接管，系统实际的IP设置以 interfaces 中的为准。反之，以 nm 中的为准。
2. 当 nm 停止接管的时候，网络连接图标就丢失了。
3. 同样是接管的问题。如果用户希望在Desktop版本中，直接使用 interfaces 进行网络配置，那最好删除 network-manager 。

network-manager重新接管

如果在出现上述问题之后，希望能继续使用 nm 来进行网络配置，则需要进行如下操作：

```shell
sudo service network-managerstop                                        #停止 nm 服务
sudo rm/var/lib/NetworkManager/NetworkManager.state       #移除 nm 的状态文件
sudo gedit /etc/NetworkManager/nm-system-settings.conf     #打开 nm 的配置文件
## 里面有一行：managed=true
## 如果你手工改过 /etc/network/interfaces ，nm 会自己把这行改成：managed=false
## 将 false 修改成 true
sudo service network-manager start
```

## 直接修改网络 ## 

除了修改配置文件和使用管理工具，Linux还提供了命令直接修改网络地址，这些修改都是**临时修改**，一旦出现问题或重启地址会被放弃。

### 使用ifconfig命令 ###

```shell
#设置IP和掩码
sudo ifconfig eth0 192.168.1.4 netmask 255.255.255.0
#设置网关
sudo route add default gw 192.168.1.1
```

### 使用ip命令 ### 

```shell
# 增加一个ip 地址
sudo ip add add 192.168.1.4/24 dev eth0:0
# 删除一个ip地址
sudo ip add del  192.168.1.4/24 dev eth0:0
# 添加一条路由
sudo ip route add 192.168.1.0/24 via 192.168.1.1 dev eth0
# 删除一条路由
sudo ip route del 192.168.1.0/24
```