---
title: windows通过虚拟机使用docker
categories:
- 后端
tags:
- php
- laravel
---

## 1、centos yum安装docker
#### 卸载旧版本
```bash
$ sudo yum remove docker \
  docker-common \
  docker-selinux \
  docker-engine
```
#### 安装依赖包
```bash
$ sudo yum install -y yum-utils \
   device-mapper-persistent-data \
   lvm2
```
#### 添加yum源
```bash
#国内源
$ sudo yum-config-manager \
--add-repo \
https://mirrors.ustc.edu.cn/docker-ce/linux/centos/docker-ce.repo

# 官方源
# $ sudo yum-config-manager \
# --add-repo \
# https://download.docker.com/linux/centos/docker-ce.repo
```
#### 安装Docker CE
```bash
#安装最新版本的 Docker CE
$ sudo yum-config-manager --enable docker-ce-edge
#安装测试版本的 Docker CE
$ sudo yum-config-manager --enable docker-ce-test
#正式安装
$ sudo yum makecache fast
$ sudo yum install docker-ce
```
## 2、脚本自动安装
```bash
$ curl -fsSL get.docker.com -o get-docker.sh
$ sudo sh get-docker.sh --mirror Aliyun
```
## 3、镜像加速
方式有很多，这里使用的是163镜像(这个效果好像没有daocloud加速器好)
```bash
$ vim /etc/docker/daemon.json
#添加
{
    "registry-mirrors": [
        "http://hub-mirror.c.163.com"
    ]
}
#保存重启docker
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
```
## 4、docker-compose 安装
```bash
$ sudo curl -L https://github.com/docker/compose/releases/download/1.19.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ sudo chmod +x /usr/local/bin/docker-compose
```
至此，docker安装工作已经完成，因为最终目的是让虚拟机作为一个工作环境，而代码编写、调试在windows上，所以我们需要虚拟机和windows文件共享，同步更新，这样一个完整的工作流程就ok了
接下来，实现虚拟机文件共享
## [在 Linux 虚拟机中手动安装 VMware Tools](https://docs.vmware.com/cn/VMware-Workstation-Pro/14.0/com.vmware.ws.using.doc/GUID-08BB9465-D40A-4E16-9E15-8C016CC8166F.html)
在主机上，从 Workstation Pro 菜单栏中选择虚拟机 > 安装 VMware Tools。
如果安装了早期版本的 VMware Tools，则菜单项是更新 VMware Tools。
```bash
#如果未装载 VMware Tools 虚拟 CD-ROM 映像，请装载 CD-ROM 驱动器。
#如果装载点目录尚不存在，请创建该目录。
$ mkdir /mnt/cdrom
#某些 Linux 发行版使用不同的装载点名称。例如，某些发行版上的装载点是 /media/VMware Tools 而不是 #/mnt/cdrom。请修改该命令以反映您的发行版使用的约定。

#装载 CD-ROM 驱动器。
#某些 Linux 发行版使用不同的设备名称，或者以不同的方式组织 /dev 目录。如果 CD-ROM 驱动器不是 #/dev/cdrom 或 CD-ROM 装载点不是 /mnt/cdrom，则必须修改该命令以反映您的发行版使用的约定。
$ mount /dev/cdrom /mnt/cdrom

#（可选）在安装 VMware Tools 之前，删除以前的 vmware-tools-distrib 目录。
#该目录的位置取决于以前安装时的存储位置。通常，该目录位于 /tmp/vmware-tools-distrib。
#列出挂载点目录的内容，并记下 VMware Tools tar 安装程序的文件名。
$ ls mount-point
#解压缩安装程序。
$ tar zxpf /mnt/cdrom/VMwareTools-x.x.x-yyyy.tar.gz -C /tmp/  #x.x.x 值是产品版本号，yyyy 是产品版本的内部版本号。
# 如果需要，请卸载 CD-ROM 映像。
$ umount /dev/cdrom 
#如果 Linux 发行版自动装载 CD-ROM，则不需要卸载该映像。

#运行安装程序并以 root 用户身份配置 VMware Tools。
$ cd /tmp/vmware-tools-distrib
$ sudo ./vmware-install.pl
```
接下来就是根据提示键y/n

## [在 Linux 客户机中装载共享文件夹](https://docs.vmware.com/cn/VMware-Workstation-Pro/14.0/com.vmware.ws.using.doc/GUID-D6D9A5FD-7F5F-4C95-AFAB-EDE9335F5562.html)
![](/images/screenshot.png)


## [在 Linux 客户机中装载共享文件夹](https://docs.vmware.com/cn/VMware-Workstation-Pro/14.0/com.vmware.ws.using.doc/GUID-AB5C80FE-9B8A-4899-8186-3DB8201B1758.html)
```bash
#查看当前虚拟机设置共享的文件夹
$ vmware-hgfsclient  //根据上图配置的共享文件夹，这条命令会显示docker
$ mount -t vmhgfs .host:/docker /mnt/hgfs  //docker换成你vmware-hgfsclient执行结果
#如果报错Error: cannot mount filesystem: No such device 执行：
$ sudo vmhgfs-fuse .host:/docker /mnt/hgfs 即可（有时候这个报错并不影响共享，查看/mnt/hgfs目录已经有了共享文件夹）
```