---
title: Samba
date: '2023-05-19'
type: book
weight: 30
highlight: true
tags:
  - current
---


## Samba 网络文件共享服务

- 简介

> 早期网络想要在不同主机之间共享文件大多要用FTP协议来传输，但FTP协议仅能做到传输文件却不能直接修改对方主机的资料数据，这样确实不太方便，于是便出现了NFS开源文件共享程序:NFS(NetworkFile System)是一个能够将多台Linux的远程主机数据挂载到本地目录的服务，属于轻量级的文件共享服务，不支持Linux与 Windows系统间的文件共享。

## 安装samba
```bash
# ubuntu
sudo apt install samba
# CentOS
sudo yum install samba -y
```

## 检测是否安装成功

`whereis  samba`

## 启动samba服务
`sudo service smb start`

## 设置开机自启动
`sudo systemctl enable smb`

## 停止samba
`sudo systemctl stop smb`


## 创建共享文件夹
`mkdir -p /home/m/Shared_Folder`

## 设置权限
`sudo chmod 777 /home/m/Shared_Folder`

## 配置samba
```bash
cd /etc/samba/
m@m:/etc/samba$ ls
lmhosts  smb.conf  smb.conf.example

# 修改配置并添加共享信息
sudo vim smb.conf

# 这里是新添加的信息


[Sphinx_dir]
        comment = Sphinx
        path = /home/m/Shared_Folder
        writable = yes
        browseable = yes
        read only = no
        public = yes
        admin users = @root
        valid users = @root
        create mask = 0777
        directory mask = 0777
```

## 创建用户密码

```bash
# 创建samba用户
sudo useradd -s /sbin/nologin  demo

# 为samba用户创建密码
sudo smbpasswd -a demo

# 重启samba
sudo systemctl restart smb
```

## 配置防火墙通过

```bash

sudo firewall-cmd --permanent --zone=public --add-service=samba
success
sudo firewall-cmd --reload
success

```
## Windows 访问
win+e 键 打开文件夹 输入\\+ip


