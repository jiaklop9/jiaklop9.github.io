---
title: 'Docker: 银河麒麟操作系统 v10 中离线安装'
date: 2025-05-14 09:36:54
tags: docker
categories: docker
---

# 银河麒麟操作系统 v10 中离线安装 Docker


## 1. 查看系统版本
```shell
[root@localhost opt]# cat /etc/os-release
NAME="Kylin Linux Advanced Server"
VERSION="V10 (Lance)"
ID="kylin"
VERSION_ID="V10"
PRETTY_NAME="Kylin Linux Advanced Server V10 (Lance)"
ANSI_COLOR="0;31"
[root@localhost opt]#
```
## 2. 查看Linux内核版本
```shell
[root@localhost opt]# uname -r
4.19.90-52.22.v2207.ky10.aarch64
[root@localhost opt]# uname -a
Linux localhost.localdomain 4.19.90-52.22.v2207.ky10.aarch64 #1 SMP Tue Mar 14 11:52:45 CST 2023 aarch64 aarch64 aarch64 GNU/Linux
[root@localhost opt]# 
```
## 3. 查看iptables版本
```shell
[root@localhost opt]# iptables --version
iptables v1.8.5 (legacy)
```
## 4. 判断处理器架构
```shell
[root@localhost opt]# uname -p
aarch64
[root@localhost opt]# 
```
>可以看到，系统处理器架构为 ARM 架构；如果为 x86 架构的，则会显示 x86_64;
## 5. 离线下载docker包
```shell
https://download.docker.com/linux/static/stable/
```
## 6. 解压并移动
```shell
tar -zxvf docker-20.10.6.tgz
mv docker/* /usr/bin/
```
## 7.  配置docker文件
```shell
//新建配置文件
vi /usr/lib/systemd/system/docker.service

//docker.service文件中写入如下内容
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target
[Service]
Type=notify
ExecStart=/usr/bin/dockerd
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s
[Install]
WantedBy=multi-user.target

// 给与可执行权限
chmod +x /usr/lib/systemd/system/docker.service
```

## 8.  开机启动docker
```shell
systemctl enable docker
```

## 9. 查看docker信息
```shell
docker info
```


