---
title: Virtualbox 虚拟集群搭建
date: 2017-02-07 10:46:42
tags:
    - 架构
    - 运维
---

## 需求

## 环境说明

## 环境搭建

### gateway

### SNAT 网关

禁用`firewalld`，至于为啥因为我还不会用`firewalld`。

```bash
# systemctl status firewalld

# systemctl stop firewalld

# systemctl disable firewalld
```

安装 iptables

```bash
# yum install -y iptables.x86_64 iptables-services.x86_64

# systemctl start iptables

# systemctl status iptables
```

开启ip转发

```bash
# echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf

# iptables -t nat -A POSTROUTING -s 10.0.0.0/8 -j SNAT --to <gateway ip>
```

保存重启iptables

```bash
# iptables-save > /etc/sysconfig/iptables

# systemctl restart iptables

# systemctl status iptables
```

### DNS

```bash
# yum install -y dnsmasq.x86_64
```

### yum repo

### docker mirror

## 常用操作优化

### 启动集群

### 创建集群

### 网络设置

## 常用工具

### vim

### zsh

### tmux

### ansible

非常好用的集群管理工具，详情参考另一篇博文[《Ansible 学习之路》](/2017/02/07/Learning-Ansible/)
