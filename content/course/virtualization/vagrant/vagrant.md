---
title: Vagrant
date: '2022-10-16'
type: book
weight: 20
---

## Vagrant 简介
 
## 安装
[Vagrant官网](https://www.vagrantup.com/)

[Vagrant镜像地址](https://app.vagrantup.com/boxes/search)

## 使用
```bash
# 创建虚拟机
vagrant up
# 查看虚拟机状态
vagrant status
# 查看本地虚拟机
vagrant box list
# 下载虚拟机镜像可选择不同虚拟机平台的系统镜像文件
vagrant box add generic/ubuntu2004
# 指定需要下载的虚拟机平台和系统镜像版本
vagrant box add generic/ubuntu2004 --provider=virtualbox --box-version=3.1.22
# 使用ssh进入虚拟机中
vagrant ssh <host name>
# 查看虚拟机配置
vagrant ssh-config 
or
vagrant ssh-config <host name>
# 暂停虚拟机
# 暂停
vagrant suspend <host name>
# 重新开始
vagrant resume <host name>
# 重新加载
vagrant reload <host name>
# 关机
vagrant halt <host name>

# 删除虚拟机
vagrant destroy <host name>
# 查看全部虚拟机的状态
vagrant global-status
# 清除无效的虚拟机缓存条目
vagrant global-status --prune

# 指定平台和版本号进行删除本地镜像文件
vagrant box remove --provider=hyperv --box-version=3.1.22
# 退出虚拟机
logout

# vagrant 命令的使用
## 初始化box的操作，会生成vagrant的配置文件Vagrantfile
vagrant init

## 启动本地环境虚拟机
vagrant up --provision 

## 重载虚拟机
vagrant reload --provision

## 销毁当虚拟机
vagrant destroy

## 查看当前虚拟机运行状态
vagrant status 

## 唤醒虚拟机
vagrant resume 

## 挂起虚拟机
vagrant suspend

## SSH 至虚拟机中
vagrant ssh  

## 重启虚拟机
vagrant reload

## 关闭虚拟机  
vagrant halt

## 添加box的操作
vagrant box  add

## 查看当前已经添加的box列表
vagrant box list

## 删除相应的box
vagrant box remove

## 打包命令、可以把当前运行的虚拟机环境进行打包
vagrant package

## 用于安装卸载插件
vagrant plugin

## 显示当前用户Vagrant的所有环境状态
vagrant global-status

# vagrant 插件推荐
## vagrant中推荐文件共享方式
- 如果可能，我们建议使用NFS而不是VirtualBox共享文件夹。您还可以使用vagrant-sshfs插件，该插件与NFS不同，可在所有操作系统上使用
## 作用：完成虚拟机间的数据传送


## 使用插件
- 在宿主机上使用

vagrant scp ./a.txt node-1:/vagrant/a.txt

## vagrant 网路配置详情

[vagrant 网路](https://www.ityoudao.cn/posts/vagrant-network/)

### 命令行查询VBox虚拟机中的ip地址
- 先查询需要查看ip的虚拟机名称

VBoxManage list runningvms
- 再查看对应虚拟机的ip地址

VBoxManage guestproperty enumerate slaver1-51| grep "Net.*V4.*IP"
```
---

## 安装插件
[vagrant-scp使用方法](https://github.com/invernizzi/vagrant-scp)

```bash
vagrant plugin install vagrant-scp

vagrant scp file.txt :file.txt
```