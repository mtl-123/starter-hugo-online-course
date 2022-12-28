---
title: Vagrantfile 模板编写
date: '2022-10-23'
type: book
weight: 20
---
## 编写Vagrant脚本

> 使用Vagrantfile 批量创建虚拟机

### 写法一

> 创建一个名称为 Vagrantfile 的文件并写入下面脚本信息

```ruby
# 使用host_list 批量定义不同的主机名称与虚拟机操作系统
host_list = [

    {
        :name => "host1",
        :box => "centos/7"
    
    },
    {
        :name => "host2",
        :box => "centos/7"
    },
    {
        :name => "host3",
        :box => "centos/8"
    }

]

# 循环创建虚拟机主机
Vagrant.configure("2") do | config|
    host_list.each do | item|
         config.vm.define item[:name] do|host|
         # 配置宿主机文件同步
         config.vm.synced_folder "宿主机文件路径","系统路径名称", type: "rsync"
            # 设置虚拟机的Box操作系统    
            host.vm.box = item[:box]            
         end
    end
 end

```

---

### 写法二

```ruby
Vagrant.configure("2") do | config|
 (1..3).each do |i|
  config.vm.define "k8s-node#{i}" do |node|
   # 设置虚拟机的Box
   node.vm.box = "centos/7"

   # 设置虚拟机的主机名
   node.vm.hostname="k8s-node#{i}"

   # 设置虚拟机的IP
   node.vm.network "private_network", ip: "192.168.56.#{99+i}", netmask: "255.255.255.0"

   # 设置主机与虚拟机的共享目录
   # node.vm.synced_folder "~/Documents/vagrant/share", "/home/vagrant/share"

   # VirtaulBox相关配置
   node.vm.provider "virtualbox" do |v|
    # 设置虚拟机的名称
    v.name = "k8s-node#{i}"
    # 设置虚拟机的内存大小
    v.memory= 4096
    # 设置虚拟机的CPU个数
    v.cpus = 4
   end
  end
 end
end

```

---

### 写法三

> 创建不同的主机名称

```ruby
# vi: set ft=ruby :

servers = [
    {
        :name => "gitlab",
        :type => "master",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.10",
        :mem => "4094",
        :cpu => "2"
    },
    {
        :name => "jenkins",
        :type => "node",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.11",
        :mem => "4094",
        :cpu => "2"
    },
    {
        :name => "k8s-master",
        :type => "node",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.12",
        :mem => "4094",
        :cpu => "2"
    }
]

Vagrant.configure("2") do |config|
    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network :private_network, ip: opts[:eth1]
            config.vm.provider "virtualbox" do |v|
                v.name = opts[:name]
                v.customize ["modifyvm", :id, "--groups", "/DevOps Environment"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
            end
        end
    end
end

```

### 创建k8s集群

[批量创建k8s集群](https://medium.com/@raj10x/multi-node-kubernetes-cluster-with-vagrant-virtualbox-and-kubeadm-9d3eaac28b98)

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

servers = [
    {
        :name => "k8s-head",
        :type => "master",
        :box => "ubuntu/xenial64",
        :box_version => "20180831.0.0",
        :eth1 => "192.168.205.10",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-1",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20180831.0.0",
        :eth1 => "192.168.205.11",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-2",
        :type => "node",
        :box => "ubuntu/xenial64",
        :box_version => "20180831.0.0",
        :eth1 => "192.168.205.12",
        :mem => "2048",
        :cpu => "2"
    }
]

# This script to install k8s using kubeadm will get executed after a box is provisioned
$configureBox = <<-SCRIPT

    # install docker v17.03
    # reason for not using docker provision is that it always installs latest version of the docker, but kubeadm requires 17.03 or older
    apt-get update
    apt-get install -y apt-transport-https ca-certificates curl software-properties-common
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key add -
    add-apt-repository "deb https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") $(lsb_release -cs) stable"
    apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 17.03 | head -1 | awk '{print $3}')

    # run docker commands as vagrant user (sudo not required)
    usermod -aG docker vagrant

    # install kubeadm
    apt-get install -y apt-transport-https curl
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
    apt-get update
    apt-get install -y kubelet kubeadm kubectl
    apt-mark hold kubelet kubeadm kubectl

    # kubelet requires swap off
    swapoff -a

    # keep swap off after reboot
    sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`
    # set node-ip
    sudo sed -i "/^[^#]*KUBELET_EXTRA_ARGS=/c\KUBELET_EXTRA_ARGS=--node-ip=$IP_ADDR" /etc/default/kubelet
    sudo systemctl restart kubelet
SCRIPT

$configureMaster = <<-SCRIPT
    echo "This is master"
    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`

    # install k8s master
    HOST_NAME=$(hostname -s)
    kubeadm init --apiserver-advertise-address=$IP_ADDR --apiserver-cert-extra-sans=$IP_ADDR  --node-name $HOST_NAME --pod-network-cidr=192.168.0.0/16

    #copying credentials to regular user - vagrant
    sudo --user=vagrant mkdir -p /home/vagrant/.kube
    cp -i /etc/kubernetes/admin.conf /home/vagrant/.kube/config
    chown $(id -u vagrant):$(id -g vagrant) /home/vagrant/.kube/config

    # install Calico pod network addon
    export KUBECONFIG=/etc/kubernetes/admin.conf
    kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/rbac-kdd.yaml
    kubectl apply -f https://docs.projectcalico.org/v3.1/getting-started/kubernetes/installation/hosted/kubernetes-datastore/calico-networking/1.7/calico.yaml

    kubeadm token create --print-join-command >> /etc/kubeadm_join_cmd.sh
    chmod +x /etc/kubeadm_join_cmd.sh

    # required for setting up password less ssh between guest VMs
    sudo sed -i "/^[^#]*PasswordAuthentication[[:space:]]no/c\PasswordAuthentication yes" /etc/ssh/sshd_config
    sudo service sshd restart

SCRIPT

$configureNode = <<-SCRIPT
    echo "This is worker"
    apt-get install -y sshpass
    sshpass -p "vagrant" scp -o StrictHostKeyChecking=no vagrant@192.168.205.10:/etc/kubeadm_join_cmd.sh .
    sh ./kubeadm_join_cmd.sh
SCRIPT

Vagrant.configure("2") do |config|

    servers.each do |opts|
        config.vm.define opts[:name] do |config|

            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network :private_network, ip: opts[:eth1]

            config.vm.provider "virtualbox" do |v|

                v.name = opts[:name]
            	 v.customize ["modifyvm", :id, "--groups", "/Ballerina Development"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
                v.customize ["modifyvm", :id, "--cpus", opts[:cpu]]

            end

            # we cannot use this because we can't install the docker version we want - https://github.com/hashicorp/vagrant/issues/4871
            #config.vm.provision "docker"

            config.vm.provision "shell", inline: $configureBox

            if opts[:type] == "master"
                config.vm.provision "shell", inline: $configureMaster
            else
                config.vm.provision "shell", inline: $configureNode
            end

        end

    end

end 

```
---

## 远程脚本安装K8s
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "master" do |dev|
  config.vm.box = "gbailey/amzn2"
  config.vm.box_version = "20221023.0.0"
  dev.vm.provider "virtualbox" do |v|
    v.memory= 4096
    v.cpus = 2
    
  config.vm.provision "B", type: "shell", inline:<<-SHELL
  
  echo -e "\033[33m download scritp file \033[0m"
   wget https://raw.githubusercontent.com/mtl-123/kubernetes/mei/script/install_ctr_k8s.sh
  echo -e "\033[31m Add executable permissions \033[0m"
  chmod +x install_ctr_k8s.sh
  echo "================================== start install k8s ============================================= "
  sudo ./install_ctr_k8s.sh

  echo -e "\033[32m Kubernetes install successful \033[0m"
  
  SHELL
  end
end
end
```
