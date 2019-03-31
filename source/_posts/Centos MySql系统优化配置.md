---
title: Centos MySql系统优化配置
categories:
- 数据库
tags:
- MySql
---
## 内核相关参数（/etc/sysctl.conf）
```shell
#连接数
net.core.somaxconn=65535
net.core.netdev_max_backlog=65535
net.ipv4.tcp_max_syn_backlog=65535
#加快tcp连接回收配置
net.ipv4.tcp_fin_timeout=10
net.ipv4.tcp_tw_reuse=1
net.ipv4.tcp_tw_recycle=1
#接受和发送缓冲区大小的默认值和最大值
net.core.wmem_default=87380
net.core.wmem_max=16777216
net.core.rmem_default=87380
net.core.rmem_max=16777216
#减少tcp失效连接占用系统资源
net.ipv4.tcp_keepalive_time=120
net.ipv4.tcp_keepalive_intvl=30
net.ipv4.tcp_keepalive_probes=3
#交换分区对数据库的配置
vm.swappiness=0
```
## 资源限制参数（/etc/security/limit.conf）
```shell
# 打开文件数限制（* 表示对所有用户有效；soft指的是当前系统生效的设置；hard系统中所能设置的最大值；nofile表示所限制的资源是打开文件的最大数 ）
* soft nofile 65535
* hard nofile 65535
```
## 磁盘调度策略（deadline）
```shell
echo deadline > /sys/block/sda/queue/scheduler
```


