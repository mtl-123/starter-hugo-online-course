---
title: zabbix_agent2
date: '2023-08-09'
type: book
weight: 20
---

## 安装zabbix agent2


```bash

# zabbix agent2 install 

# 配置源
rpm -Uvh https://repo.zabbix.com/zabbix/5.0/rhel/7/x86_64/zabbix-release-5.0-1.el7.noarch.rpm

# 安装zabbix agent2
yum install zabbix-agent2 -y

# 查看配置
egrep -v '#|^$' /etc/zabbix/zabbix_agent2.conf

# 修改配置
vim /etc/zabbix/zabbix_agent2.conf

#修改
Server=127.0.0.1,192.168.1.130    #服务器ip
ServerActive=127.0.0.1,192.168.1.130   #服务器ip
Hostname=Zabbix agent 2     #自己改个名字

# 开机自启动
systemctl enable zabbix-agent2.service
# 启动
systemctl start zabbix-agent2.service
# 停止
systemctl stop zabbix-agent2.service
# 状态
systemctl  status zabbix-agent2.service

```

## 自动发现，自动注册
- 什么是自动发现，自动注册
 - 自动发现：  zabbix server 主动的去发现所有的客户端，然后将客户端的信息，登记在服务端上。
root@gitlab:~# systemctl is-active zabbix-agent2
active
