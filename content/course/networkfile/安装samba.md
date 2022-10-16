---
title: Samba
date: '2022-10-16'
type: book
weight: 30
highlight: true
tags:
  - current
---


## Samba 网络文件共享服务

### CentOS系统安装
```bash
# 安装
sudo yum install samba -y

# 启动
sudo service smb start
[sudo] m 的密码：
Redirecting to /bin/systemctl start smb.service

# 设置开机自启动
sudo systemctl enable smb
Created symlink /etc/systemd/system/multi-user.target.wants/smb.service → /usr/lib/systemd/system/smb.service.

# 停止samba
sudo systemctl stop smb

# 创建共享文件夹
mkdir -p /home/m/Shared_Folder

# 设置权限
sudo chmod 777 /home/m/Shared_Folder

# 配置samba
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

# 为samba用户创建密码
sudo smbpasswd -a root

# 重启samba
sudo systemctl restart smb
```

### 配置防火墙通过
```bash

sudo firewall-cmd --permanent --zone=public --add-service=samba
success
sudo firewall-cmd --reload
success

```
### Windows 访问
win+e 键 打开文件夹 输入\\+ip

[](windws.jpg)