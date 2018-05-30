---
layout: post
title:  "将树莓派变成Kubernetes的一个节点"
categories: Raspberry
tags:  Raspberry Linux
author: SurprisedCat
---

* content
{:toc}

## 前提 ##

我在自己的主机上搭建了一个K8S集群，一个master节点，两个node节点，手边还有三个树莓派，想把他们也并到集群中。昨天我在docker中安装了docker，而kubernetes官网上也有arm的二进制文件，今天打算将这些文件配置到raspberry上，使之成为K8S集群的新节点。

**需要指出的是**，我原来的集群是X86_64架构的机器，raspberry是arm架构的，他们用的是不一样的镜像，在master节点需要指定不同架构的镜像给不同的node节点。

## 下载kubernetes node节点 ##

本人使用的是k8s 1.8版本的（master节点是这个版本）$^{[1]}$
![K8S node 节点]( /assets/k8s-nodes.jpg)

解压后新建文件夹：

```shell
sudo mkdir -p /opt/kubernetes/{bin,cfg}
sudo mv kubelet kubectl kube-proxy /opt/kubernetes/bin/
```

## 编写Kubernetes配置文件 ##

### 创建kubeconfig配置文件 ###

```shell
sudo vim /opt/kubernetes/cfg/kubelet.kubeconfig
apiVersion: v1
kind: Config
clusters:
  - cluster:
      server: http://{master 节点 IP}:8080
    name: local
contexts:
  - context:
      cluster: local
    name: local
current-context: local
```

创建配置文件kubeconfig用于kubelet连接master apiserver。

### 创建自身配置文件 ### 

```shell
sudo vim /opt/kubernetes/cfg/kubelet            
# 启用日志标准错误
KUBE_LOGTOSTDERR="--logtostderr=true"
# 日志级别
KUBE_LOG_LEVEL="--v=4"
# Kubelet服务IP地址
NODE_ADDRESS="--address={本机IP}"
# Kubelet服务端口
NODE_PORT="--port=10250"
# 自定义节点名称
NODE_HOSTNAME="--hostname-override={本机IP}"
# kubeconfig路径，指定连接API服务器
KUBELET_KUBECONFIG="--kubeconfig=/opt/kubernetes/cfg/kubelet.kubeconfig"
# 允许容器请求特权模式，默认false
KUBE_ALLOW_PRIV="--allow-privileged=false"
# DNS信息
KUBELET_DNS_IP="--cluster-dns=172.16.76.2"
KUBELET_DNS_DOMAIN="--cluster-domain=cluster.local"
# 禁用使用Swap
KUBELET_SWAP="--fail-swap-on=false"
```

### 创建systemd服务文件 ###

```shell
sudo vim /lib/systemd/system/kubelet.service
[Unit]
Description=Kubernetes Kubelet
After=docker.service
Requires=docker.service
[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kubelet
ExecStart=/opt/kubernetes/bin/kubelet \
${KUBE_LOGTOSTDERR} \
${KUBE_LOG_LEVEL} \
${NODE_ADDRESS} \
${NODE_PORT} \
${NODE_HOSTNAME} \
${KUBELET_KUBECONFIG} \
${KUBE_ALLOW_PRIV} \
${KUBELET_DNS_IP} \
${KUBELET_DNS_DOMAIN} \
${KUBELET_SWAP}
Restart=on-failure
KillMode=process
[Install]
WantedBy=multi-user.target

# systemctl daemon-reload
# systemctl enable kubelet
# systemctl restart kubelet
```

### 创建kube-proxy配置文件 ###

```shell
sudo  vim /opt/kubernetes/cfg/kube-proxy   
# 启用日志标准错误
KUBE_LOGTOSTDERR="--logtostderr=true"
# 日志级别
KUBE_LOG_LEVEL="--v=4"
# 自定义节点名称
NODE_HOSTNAME="--hostname-override={本机IP}"
# API服务地址
KUBE_MASTER="--master=http://{master 节点 IP}:8080"
```

### 创建proxy的systemd服务文件 ###

```shell
sudo vim /lib/systemd/system/kube-proxy.service
[Unit]
Description=Kubernetes Proxy
After=network.target
[Service]
EnvironmentFile=-/opt/kubernetes/cfg/kube-proxy
ExecStart=/opt/kubernetes/bin/kube-proxy \
${KUBE_LOGTOSTDERR} \
${KUBE_LOG_LEVEL} \
${NODE_HOSTNAME} \
${KUBE_MASTER}
Restart=on-failure
[Install]
WantedBy=multi-user.target
```
最后将/opt/kubernetes/bin 放到PATH变量中。


## 遇到问题 ##

> Failed to start ContainerManager system validation failed - Following Cgroup subsystem not mounted: [memory]  

新建文件/etc/default/grub,添加：$^{[2]}$

>GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"  

```shell
sudo update-grub 
# 如果无法找到 update-grub 命令，你可以通过下面的命令安装它。
sudo apt-get install grub2-common 
```

或者更有效的是，在/boot/cmdline.txt之后加入
cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1  
再reboot。就可以了。$^{[3]}$



## 参考文献 ## 

[1][Kubernetes Github](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG-1.8.md#node-binaries)

[2][如何在linux上通过GRUB添加内核参数](https://linux.cn/article-2268-1.html)

[3][树莓派论坛](https://www.raspberrypi.org/forums/viewtopic.php?p=1312253)