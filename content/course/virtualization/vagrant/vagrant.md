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

### 插件安装

`vagrant plugin install vagrant-scp`

```bash
示例如下：
------------------------------------------------
命令    参数   本地文件            虚拟机名称:~/传输到虚拟机文件夹位置
vagrant scp    local_file.txt     vm_name:~/
```

### 详解

- vagrant： 命令
- scp ：参数
- local_file.txt : 本地文件
- vm_name: 虚拟机名称
- ~/: 传输到虚拟机中的绝对路径位置


# vagrant 常用命令

## 查看
```bash
# 查看下载到本地的box镜像
vagrant box list
# 查看已经创建的box虚拟机配置文件详情
vagrant ssh-config
#
vagrant global-status --prune
```
## 初始化
```bash
# 指定box镜像初始化,会得到一个 Vagrantfile文件，里面使用的基础box是初始化指定的box镜像名
vagrant init centos-vim
```
## 启动
```bash
# 启动所有虚拟机
vagrant up
# 指定某台虚拟机启动
vagrant up vm_name
# 指定box虚拟化平台并启动
vagrant up --provider virtualbox
```
## 停止
```bash
# 关机所有box
vagrant halt
# 指定某台box关机
vagrant halt vm_name
```
## 删除
```bash
# 删除box镜像
vagrant box remove box_name
# 强制删除所有创建的box虚拟机
vagrant destroy --force
# 指定某台box虚拟机进行删除
vagrant destroy vm_name --force
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
vagrant  box add --name centos-vim --provider=virtualbox ./package.box
# 查看创建的centos-vim的基础box
vagrant box list
```
## 共享box到

vim metadata.json

```json
{"provider": "virtualbox"}
```
## 进入box内部
vagrant ssh box

## 插件
### scp
```bash
# 作用： 复制宿主机文件到box内部
# 安装
vagrant plugin install vagrant-scp
# 示例：
vagrant scp local_file vm_name:~/remote_file_path
```


## Vagrantfile

```ruby
Vagrant.configure("2") do |config|
  # 定义虚拟机 do ||
  config.vm.box = "ubuntu/xenial64"do |jenkins|
  # 定义虚拟机平台
  config.vm.provider = "virtualbox"
  config.vm.provision :shell, path: "bootstrap.sh"

end
```
  
