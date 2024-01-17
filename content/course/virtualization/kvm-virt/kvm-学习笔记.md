---
title: KVM-Virt学习笔记
date: '2023-05-10'
type: book
weight: 20
---

{{< toc hide_on="x1" >}}

[virtual 文档推荐](https://www.onitroad.com/jc/linux/find-ip-address-of-linux-kvm-guest-virtual-machine.html)

# KVM-virt学习笔记

## 简介

> 操作系统CentOS8.5 

```bash
# CentOS 8.5 新安装执行执行yum update 报错无法安装软件修改方式如下
cd /etc/yum.repos.d/
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
yum makecache

yum clean all

```
 

### 安装KVM
``` bash

cat /proc/cpuinfo | egrep "vmx|svm"

lscpu | grep Virtualization

lsmod | grep kvm

sudo yum update -y

sudo yum install @virt -y

sudo systemctl enable --now libvirtd

sudo yum -y install virt-top  libvirt-devel \
virt-manager libguestfs-tools bridge-utils

```

### 配置网络

[kvm 配置虚拟网桥](https://www.cnblogs.com/spec-dog/p/11178181.html)

#### CentOS 8.5 创建网桥
进入网络配置目录
`cd /etc/sysconfig/network-scripts`
这是需要创建的虚拟网桥ifcfg-br0  一下创建完成后需要激活一下
vim ifcfg-br0  

```bash
STP=yes
BRIDGING_OPTS=priority=32750
TYPE=Bridge
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=dhcp
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=br0
UUID=bdf24491-b609-4446-9554-4b17920c47cd
DEVICE=br0
ONBOOT=yes
AUTOCONNECT_SLAVES=yes

```

这是真是物理网卡，需要被修改如下
vim ifcfg-enp4s0

```bash
TYPE=Ethernet
# 这里是enp4s0物理网卡的MAC地址，切记字母一定要大写
HWADDR=18:31:BF:AF:45:A1
# 这里是宿主机物理网卡名称
NAME=enp4s0
UUID=af3dd3e0-0c49-4132-b310-c54776da9a13
ONBOOT=yes
# 这里是上面创建的虚拟网桥名称
BRIDGE=br0
```
配置生效后如下，注意：创建成功后物理网卡enp4s0是没有IP地址的br0才有
```bash
br0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.0.0.11  netmask 255.255.255.0  broadcast 10.0.0.255
        inet6 fe80::5cbc:e499:eef6:a781  prefixlen 64  scopeid 0x20<link>
        ether 18:31:bf:af:45:a1  txqueuelen 1000  (Ethernet)
        RX packets 338389  bytes 46885791 (44.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 274654  bytes 200502057 (191.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

enp4s0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        ether 18:31:bf:af:45:a1  txqueuelen 1000  (Ethernet)
        RX packets 485511  bytes 231999661 (221.2 MiB)
        RX errors 0  dropped 8  overruns 0  frame 0
        TX packets 397756  bytes 218707974 (208.5 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
#### Ubuntu 21.04 创建网桥
```bash
 sudo nmcli connection add type bridge autoconnect yes con-name br0 ifname br0

 sudo nmcli connection modify br0 ipv4.addresses 192.168.1.200/24 ipv4.gateway 192.168.1.137 ipv4.dns "8.8.8.8 192.168.0.1" ipv4.method manual
 
 sudo nmcli connection add type bridge-slave autoconnect yes master br0 ifname enx000ec6c8a31f
 
 sudo virsh attach-interface --domain <vm_name> --type bridge --source br0 --model virtio --config --live
```

brctl show
```bash
bridge name     bridge id               STP enabled     interfaces
br0             7fee.1831bfaf45a1       yes             enp4s0   # 这里就是创建成功的虚拟网桥
virbr0          8000.525400f9354f       yes             virbr0-nic
```


### 安装操作系统
```bash
sudo virt-install \
--name Harbor-Repositories  \
--ram 4096 \
--vcpus 2 \
--cdrom /home/mtl/iso/CentOS-8.5.2111-x86_64-dvd.iso  \
--disk /data/kvm/Harbor-Repositories.img,,size=100 \
--network bridge=br0 \
--vnc --vncport=5914 --vnclisten=0.0.0.0 --noautoconsole

```

### 导入.qcow2虚拟机
```bash
virt-install \
  --name win_server_2019_zy \
  --ram 4096 \
  --vcpus 2 \
  --disk path=/home/mtl/iso/win2019.qcow2 \
  --force --import --autostart \
  --vnc --vncport=5999 --vnclisten=0.0.0.0 --noautoconsole
```

### 端口
```bash
# 查询3389端口号是否开启
netstat -ntulp | grep 3389
# 查询防火墙状态
systemctl status firewalld
# 查询指定端口是否已开
firewall-cmd --query-port=3389/tcp
# 添加指定需要开放的端口
firewall-cmd --add-port=3389/tcp --permanent
#移除指定端口
firewall-cmd --permanent --remove-port=3389/tcp
```

### 远程
```bash
virsh console fed36
```

####  常用命令

```bash
# 列出所有虚拟机
virsh list --all
# 启动非活动虚拟机
virsh start virtual-name
# 正常关闭虚拟机
virsh shutdown virtual-name
# 强制关闭虚拟机
virsh destroy virtual-name
# 删除虚拟机
virsh undefine virtual-name
# 显示虚拟机基本信息
virsh dominfo virtual-name
# 编辑虚拟机配置文件
virsh edit  virtual-name
# 扩展qcow2磁盘
qemu-img resize 虚拟机名称.qcow2 +10G
# 查看 可以看到刚才扩容的空间大小
qemu-img info 虚拟机名称.qcow2
```
## 创建快照
### 查看.img磁盘信息
```bash
mtl@132:~$ sudo qemu-img info /data/kvm/MTL-DEV.img
#一下是执行qemu-img info 输出的结果。
image: /data/kvm/MTL-DEV.img
file format: qcow2  "我这里是镜像格式为qcow2 "
virtual size: 200G (214748364800 bytes)
disk size: 1.7G
cluster_size: 65536
Format specific information:
    compat: 1.1
    lazy refcounts: true
    refcount bits: 16
    corrupt: false
```
### 开始创建快照
```bash
mtl@132:~$ virsh snapshot-create MTL-DEV
Domain snapshot 1694048968 created  创建成功的输出
```
### 查看快照版本
```bash
mtl@132:~$ virsh snapshot-list  MTL-DEV
 Name                 Creation Time             State
------------------------------------------------------------
 1694048968           2023-09-07 09:09:28 +0800 shutoff

```
### KVM 虚拟机克隆
KVM虚拟机的克隆分为两种情况
 - 第一种KVM宿主机上对虚拟机直接克隆
 - 第二种通过复制配置文件的虚拟机复制克隆(适用于异机的静态迁移)
#### 方法一： KVM宿主机上对虚拟机直接克隆(需要在关机或者暂停的状态下操作)
1、查看所有的虚拟机、以及需要克隆的虚拟机的硬盘文件位置。
查看已安装的所有的KVM虚拟机
`virsh list --all`  
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/84e3eff7-f3a3-4d70-9a50-f3aef69a156a)
2、可以通过编辑需要克隆的原虚拟机配置文件，找到它磁盘文件位置
`virsh edit `
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/af12321a-f0d1-4460-bdda-98df0b037931)
如下图可看见 <source file='/data/kvm/MTL-Win10-DEV.img'/> 这就是磁盘位置
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/5865be1a-d332-4aa0-bf31-50a67c0e2b2a)
3、开始克隆、将 MTL-Win10-DEV.img 虚拟机克隆成新的虚拟机New_MTL-Win10-DEV.img、新的虚拟机的磁盘文件路径为
如果未关机开始克隆会提示如下：
ERROR    Domain with devices to clone must be paused or shutoff. **译：** 带有要克隆的设备的域必须暂停或关闭。
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/99b5a135-a0e5-4cad-a212-7f7e9a9833fd)
开始克隆
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/7860be93-853d-4b62-b65d-1be81bcab8bf)
参数详解：
![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/b1556fcf-1494-4467-899a-cb032e0561ff)

[KVM虚拟机克隆命令详解](https://www.yoyoask.com/?p=4976)


### 查看虚拟机的IP地址
方法一：
```bash
root@192:~# virsh dumpxml VM_NAME | grep "mac address" | awk -F\' '{ print }'
      <mac address='52:54:00:4b:4a:a4'/>
root@192:~# arp -an | grep 52:54:00:4b:4a:a4
? (192.168.1.181) at 52:54:00:4b:4a:a4 [ether] on br0
```
方法二：查看所有KVM虚拟机的IP地址
```bash
virsh list --name | while read n
do
  [[ ! -z $n ]] && virsh domifaddr $n
done
```
### 查询虚拟机中的IP地址
```bash
#!/bin/bash

# 设置颜色代码
GREEN=$(tput setaf 2)
RED=$(tput setaf 1)
NC=$(tput sgr0) # Reset color

# 获取所有虚拟机名称
vm_names=($(virsh list --all --name))

# 遍历虚拟机名称列表
for vm_name in "${vm_names[@]}"; do
    # 获取MAC地址
    mac_address=$(virsh dumpxml "$vm_name" | awk -F\' '/mac address/ {print $2}')

    if [ -z "$mac_address" ]; then
        echo "${RED}无法获取 $vm_name 的MAC地址${NC}"
        continue
    fi

    # 打印虚拟机名称和MAC地址
    echo "虚拟机名称: $vm_name, MAC地址: ${GREEN}$mac_address${NC}"

    # 查询真实的IP地址
    ip_address=$(arp -an | grep "$mac_address" | awk '{print $2}' | sed 's/[()]//g')

    if [ -z "$ip_address" ]; then
        echo "${RED}无法获取 $mac_address 的真实IP地址${NC}"
    else
        # 打印真实的IP地址
        echo "真实的IP地址: ${GREEN}$ip_address${NC}"
    fi
done
```




