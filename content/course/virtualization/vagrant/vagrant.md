---
title: Vagrant
date: '2022-10-23'
type: book
weight: 20
---

## Vagrant 简介

> Vagrant是一种在单一工作流中构建和管理虚拟机环境的工具。凭借易于使用的工作流和对自动化的关注。
> Vagrant降低了开发环境的设置时间，增加了产品平价，并使“在我的机器上工作”的借口成为过去的遗留问题。

[Vagrant官网](https://www.vagrantup.com/)

[Vagrant镜像地址](https://app.vagrantup.com/boxes/search)

[Vagrant命令手册](https://www.vagrantup.com/docs/cli)

[本地vagrant镜像box上传到vagrant cloud并开源使用](https://www.jianshu.com/p/a5896360493b)

## 安装

```bash
# windows 系统
https://releases.hashicorp.com/vagrant/2.3.2/vagrant_2.3.2_windows_amd64.msi

# ubuntu 系统
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install vagrant -y

# centos 系统
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install vagrant

```

---

## 常用命令

## 查看

```bash
# 下载虚拟机镜像可选择不同虚拟机平台的系统镜像文件
vagrant box add generic/ubuntu2004
# 指定需要下载的虚拟机平台和系统镜像版本
vagrant box add generic/ubuntu2004 --provider=virtualbox --box-version=3.1.22
# 查看下载到本地的box镜像
vagrant box list
# 查看已经创建的box虚拟机配置文件详情
vagrant ssh-config
# 查看所有box运行状态
vagrant status 
```

## 初始化

```bash
# 指定box镜像初始化,会得到一个 Vagrantfile文件，里面使用的基础box是初始化指定的box镜像名
vagrant init box_name
```

## 启动

```bash
# 启动所有虚拟机
vagrant up
# 启动本地所有环境虚拟机
vagrant up --provision 
# 指定某台虚拟机启动
vagrant up vm_name
# 指定box虚拟化平台并启动
vagrant up --provider virtualbox
# 唤醒虚拟机
vagrant resume  vm_name
```

## 停止

```bash
# 关机所有box
vagrant halt
# 指定某台box关机
vagrant halt vm_name
# 挂起虚拟机
vagrant suspend vm_name
```

## 加载

```bash
# 重新开始
vagrant resume vm_name
# 重新加载
vagrant reload vm_name
# 重载虚拟机
vagrant reload --provision

```

## 删除

```bash

# 删除box镜像
vagrant box remove box_name
# 指定平台和版本号进行删除本地镜像文件
vagrant box remove --provider=hyperv --box-version=3.1.22

# 强制删除所有创建的box虚拟机
vagrant destroy --force
# 指定某台box虚拟机进行删除
vagrant destroy vm_name --force
# 清除无效的虚拟机缓存条目
vagrant global-status --prune
```

## 进入box内部

```bash
# 进入指定虚拟机内部
vagrant ssh  vm_name
```

## 网络

```bash
[vagrant 网路](https://www.ityoudao.cn/posts/vagrant-network/)
```

## 打包

```bash
# 插件vagrant-vbguest
# 作用：提高box运行效率
# 安装指定版本的agrant-vbguest插件
vagrant plugin install vagrant-vbguest --plugin-version 0.21
# 把当前做好的box环境打包成box镜像，导出的box名为package.box
vagrant package --bash   vm_name
# 如何使用打包的package.box镜像
vagrant  box add --name box_name --provider=virtualbox ./package.box
# 查看创建的box_name的基础box
vagrant box list
# 查看虚拟机列表
vboxmanage list vms
# 打包
vagrant package --base box_name_1503366286622_12977--output ./ubuntu_amd64.box
--base 要打包的虚拟机名称
--output 打包后的包名
-- include 打包需要增加的文件，多个文件以逗号分隔
--vagrantfile 指定vagrantfile文件
```

## 查看virtualbox 网路配置详情

```bash
# 命令行查询VBox虚拟机中的ip地址
# 先查询需要查看ip的虚拟机名称
VBoxManage list runningvms

# 再查看对应虚拟机的ip地址
VBoxManage guestproperty enumerate slaver1-51| grep "Net.*V4.*IP"
```

---

## 插件

### scp

```bash
# 作用： 复制宿主机文件到box内部
# 安装
vagrant plugin install vagrant-scp
# 示例：
vagrant scp local_file vm_name:~/remote_file_path
# 详解
- vagrant： 命令
- scp ：参数
- local_file: 本地文件
- vm_name: 虚拟机名称
- ~/remot_file_path: 传输到虚拟机中的绝对路径位置
```

### vagrant-vbguest

```bash
# 作用：提高虚拟机效率
vagrant  plugin install  vagrant-vbguest
# 安装指定版本的vbguest插件
vagrant plugin install vagrant-vbguest --plugin-version 0.21
# 卸载插件
vagrant plugin uninstall vagrant-vbguest
```
