---
layout: post
title:  "树莓派连接Wifi配置"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

## 配置网口 ##

编辑  /etc/network/interfaces  文件，将wlan0配置成dhcp，指定配置文件。

```shell
auto wlan0
iface wlan0 inet dhcp
wpa_conf /etc/wpa_supplicant/wpa_supplicant.conf
```

## 修改wpa_supplicant.conf ##

打开/etc/wpa_supplicant/wpa_supplicant.conf文件，加入如下内容：

```shell
network={
    ssid="wifi的ssid"
    key_mgmt=WPA-PSK
    psk="wifi的密码"
    priority=5
}
network={
ssid="WiFi-name2"
psk="WiFi-password2"
priority=4
}
```

其中，priority 是指连接优先级，数字越大优先级越高（不可以是负数）。

## 连接/断开wifi ##

```shell
#连接wifi
sudo ifup wlan0
# 断开wifi
sudo ifdown wlan0
```

如果不好使的话，重启一下就可以了。

## 参考文献 ## 

[树莓派中文站](http://www.52pi.net/archives/58)