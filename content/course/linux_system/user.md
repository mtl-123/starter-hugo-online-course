---
title: Linux 用户管理
date: '2022-10-15'
type: book
weight: 20
---

<!--more-->

## who 命令
- 查看当前已登录系统的用户名称。 输出：用户名、tty号、时间日期、主机地址
```bash
vagrant@m1:~$ who
vagrant  pts/0        2022-10-15 15:22 (10.0.2.2)
```
- 只列出用户
```bash
vagrant@m1:~$ who | cut -d' ' -f1 | sort | uniq
vagrant
# 或者
vagrant@m1:~$ users
vagrant

```

{{< icon name="clock" pack="fas" >}} 每周1-2小时，持续8周

## 学习

{{< youtube rfscVS0vtbw >}}

## 测试

{{< spoiler text="列表和元组之间的区别是什么?" >}}
Lists

- Lists are mutable - they can be changed
- Slower than tuples
- Syntax: `a_list = [1, 2.0, 'Hello world']`

Tuples

- Tuples are immutable - they can't be changed
- Tuples are faster than lists
- Syntax: `a_tuple = (1, 2.0, 'Hello world')`
  {{< /spoiler >}}

{{< spoiler text="Is Python case-sensitive?" >}}
Yes
{{< /spoiler >}}

## 学习资料
[Vim-command](https://yelog.org/2017/03/06/Vim-command/)


# 服务器 BMC 服务器硬件管理工具的配置

## 安装ipmi工具
sudo yum install OpenIPMI ipmitool -y

## 查看管理口配置情况
sudo ipmitool lan print
## 查询用户配置
sudo ipmitool user list 1
## 创建用户
sudo ipmitool user  set name 3 admin

## 设置密码
sudo ipmitool user  set password 3 Your_password
## 给用户权限
sudo ipmitool channel setaccess 1 3 callin=on ipmi=on link=on privilege=4
## 查看权限
sudo ipmitool channel getaccess 1 3


