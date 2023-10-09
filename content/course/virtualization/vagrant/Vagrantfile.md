---
title: Vagrantfile 模板编写
date: '2022-10-23'
type: book
weight: 20
---
## 编写Vagrant脚本

> 使用Vagrantfile 批量创建虚拟机

### 基础语法多看
```ruby
# vi: set ft=ruby :

servers = [
    {
        :name => "gitlab",
        :type => "gitlab",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.20",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "jenkins",
        :type => "jenkins",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.21",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "Blog",
        :type => "Blog",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.30",
        :mem => "4096",
        :cpu => "2"
    }
]

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置

# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|
    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
            config.vm.provider "virtualbox" do |v|
                v.name = opts[:name]
                v.customize ["modifyvm", :id, "--groups", "/DevOps-Blog Environment"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
            # 自定义用户名和密码
            # # 'echo "vagrant:1"   这里的 "vagrant用户是默认创建的不可更改:1 是密码可更改的"
            config.vm.provision 'shell', inline: 'echo "vagrant:a" | chpasswd'
            config.vm.provision "shell", inline: <<-SHELL
            echo "开启root登录"
            echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
            # echo "开启远程登录"         
            sed -in 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
            echo "重启SSH服务"
            systemctl restart sshd.service
            echo "安装 git vim curl 工具"
            apt-get install vim git curl -y
            echo "安装成功 💯💯💯"            
            SHELL
            end
        end
    end
end

```
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
ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置


# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

# 循环创建虚拟机主机
Vagrant.configure("2") do | config|
    host_list.each do | item|
         config.vm.define item[:name] do|host|
         # 配置宿主机文件同步
         config.vm.synced_folder "宿主机文件路径","系统路径名称", type: "rsync"
            # 设置虚拟机的Box操作系统    
            host.vm.box = item[:box] 
         # 创建公共网络时指定接口
         # Specify the interface when creating the public network
         config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
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

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置

# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|
    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
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
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.20",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-1",
        :type => "node",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.21",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "k8s-node-2",
        :type => "node",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "192.168.56.22",
        :mem => "2048",
        :cpu => "2"
    }
]

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置

# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

# This script to install k8s using kubeadm will get executed after a box is provisioned
$configureBox = <<-SCRIPT

    # 更换国内apt源
    cp /etc/apt/sources.list /etc/apt/sources_init.list
    tee > /etc/apt/sources.list <<EOF
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    ## Not recommended
    # deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    # deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
EOF
    # install docker v19.03.10
    # reason for not using docker provision is that it always installs latest version of the docker, but kubeadm requires 17.03 or older
    apt-get update 

    apt-get install \
    ca-certificates \
    curl \
    gnupg \
    apt-transport-https \
    software-properties-common \
    lsb-release -y

    sudo mkdir -p /etc/apt/keyrings
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
    
    # 3. 设置存储库
    echo \
      "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    # install docker-19.03.10 
    apt-get update && apt-get install -y docker-ce=$(apt-cache madison docker-ce | grep 19.03.10 | head -1 | awk '{print $3}')

    # run docker commands as vagrant user (sudo not required)
    usermod -aG docker vagrant
    mkdir -p /etc/docker

    tee <<EOF >/etc/docker/daemon.json
    {
      "exec-opts":["native.cgroupdriver=systemd"],
    
      "registry-mirrors": [
        "https://dockerhub.azk8s.cn",
        "https://reg-mirror.qiniu.com"
      ]
    }
EOF
    # 加载
    sudo systemctl daemon-reload
    # 重启
    sudo systemctl restart docker
    # 设置自启动
    sudo systemctl enable docker

    # install kubeadm
    apt-get install -y apt-transport-https curl
    curl https://mirrors.aliyun.com/kubernetes/apt/doc/apt-key.gpg | apt-key add -

    cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
    deb https://mirrors.aliyun.com/kubernetes/apt/ kubernetes-xenial main
EOF
    
    apt-get update
    apt-get install -y kubelet=1.18.5-00 kubeadm=1.18.5-00 kubectl=1.18.5-00
    apt-mark hold kubelet kubeadm kubectl

    # kubelet requires swap off
    swapoff -a

    # keep swap off after reboot
    sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

    # ip of this box
    IP_ADDR=`ifconfig enp0s8 | grep Mask | awk '{print $2}'| cut -f2 -d:`
    # set node-ip
    sudo sed -i "/^[^#]*KUBELET_EXTRA_ARGS=/c\KUBELET_EXTRA_ARGS=--node-ip=$IP_ADDR" /etc/default/kubelet
    sudo systemctl enable --now kubelet
SCRIPT

$configureMaster = <<-SCRIPT
    # 更换国内apt源
    cp /etc/apt/sources.list /etc/apt/sources_init.list
    tee > /etc/apt/sources.list <<EOF
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    ## Not recommended
    # deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    # deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
EOF

    echo "This is master"
    # ip of this box
    IP_ADDR=`ifconfig  enp0s8 | grep mask |  awk '{print $2}'| cut -f2 -d:`

    # install k8s master
    HOST_NAME=$(hostname -s)
    # sudo kubeadm  init --config kubeadm-config.yaml   
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
    sshpass -p "vagrant" scp -o StrictHostKeyChecking=no vagrant@192.168.56.20:/etc/kubeadm_join_cmd.sh .
    sh ./kubeadm_join_cmd.sh
SCRIPT

Vagrant.configure("2") do |config|
    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
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

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置

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

## 开发环境

+ Python
+ Shell
+ Golang

```ruby
# vi: set ft=ruby :

servers = [
  {
      :name => "Python-Dev",
      :type => "master",
    #   :box => "generic/centos7",
    #   :box_version => "4.2.6",
      :box => "ubuntu/focal64",
      :box_version => "20221021.0.0",
      :eth1 => "192.168.56.100",
      :mem => "4094",
      :cpu => "2"
  },
  {
      :name => "Shell-Dev",
      :type => "node",
    #   :box => "generic/centos7",
    #   :box_version => "4.2.6",
      :box => "ubuntu/focal64",
      :box_version => "20221021.0.0",
      :eth1 => "192.168.56.110",
      :mem => "4094",
      :cpu => "2"
  },
  {
      :name => "GoLang-Dev",
      :type => "node",
    #   :box => "generic/centos7",
    #   :box_version => "4.2.6",
      :box => "ubuntu/focal64",
      :box_version => "20221021.0.0",
      :eth1 => "192.168.56.120",
      :mem => "4094",
      :cpu => "2"
  }
]
# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

$configureBox = <<-SCRIPT
    # 更换国内apt源
    cp /etc/apt/sources.list /etc/apt/sources_init.list
    # 写入国内apt源配置
    tee > /etc/apt/sources.list <<EOF
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
    deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
    ## Not recommended
    # deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
    # deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
EOF
    apt update
    apt install -y git vim wget curl python3-pip
    pip3 install --upgrade pip
    mkdir ~/.pip
    touch ~/.pip/pip.conf
    tee > ~/.pip/pip.conf <<EOF
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple 
EOF

    pip install --user pdm
    cp -rf /root/.local/bin/* /usr/local/bin/
    pdm self update
    pdm completion bash > /etc/bash_completion.d/pdm.bash-completion

SCRIPT

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置
Vagrant.configure("2") do |config|
  servers.each do |opts|
      config.vm.define opts[:name] do |config|
          config.vm.box = opts[:box]
          config.vm.box_version = opts[:box_version]
          config.vm.hostname = opts[:name]
          config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
          config.vm.provider "virtualbox" do |vb|
              vb.name = opts[:name]
              vb.customize ["modifyvm", :id, "--groups", "/DevOps Environment"]
              vb.customize ["modifyvm", :id, "--memory", opts[:mem]]
          config.vm.provision "shell", inline: $configureBox
          end
      end
  end
end

--- 

# 最新的脚本

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

servers = [
    {
        :name => "gitlab",
        :type => "gitlab",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.20",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "jenkins",
        :type => "jenkins",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.21",
        :mem => "2048",
        :cpu => "2"
    },
    {
        :name => "Blog",
        :type => "Blog",
        :box => "ubuntu/focal64",
        :box_version => "20221021.0.0",
        :eth1 => "10.0.0.22",
        :mem => "4096",
        :cpu => "2"
    }
]

ENV["LC_ALL"] = "en_US.UTF-8"
#指定vm的语言环境，缺省地，会继承host的locale配置

# 获取默认接口的名称
$default_network_interface = `ip route | awk '/^default/ {printf "%s", $5; exit 0}'`

Vagrant.configure("2") do |config|
    servers.each do |opts|
        config.vm.define opts[:name] do |config|
            config.vm.box = opts[:box]
            config.vm.box_version = opts[:box_version]
            config.vm.hostname = opts[:name]
            config.vm.network "public_network",  bridge: "#$default_network_interface",  ip: opts[:eth1]
            config.vm.provider "virtualbox" do |v|
                v.name = opts[:name]
                v.customize ["modifyvm", :id, "--groups", "/DevOps-Blog Environment"]
                v.customize ["modifyvm", :id, "--memory", opts[:mem]]
            # 自定义用户名和密码
            # Set a default password for the root user
            # 为根用户设置默认密码
            config.vm.provision "shell", inline: "echo 'root:vagrant' | sudo chpasswd"
            config.vm.provision "shell", inline: <<-SHELL

            echo "开启root登录"
            echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
            # echo "开启远程登录"
            sed -in 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config           
            echo "重启SSH服务"
            systemctl restart sshd.service

            echo "安装 git vim curl 工具"
            apt-get install vim git curl -y
            echo "安装成功 💯💯💯"
            echo "nvm管理工具"

            sudo bash -c "$(curl -fsSL https://gitee.com/RubyKids/nvm-cn/raw/main/install.sh)"

            # nvm install  v14.17.5

            SHELL
            # Configure SSH settings for all virtual machines
            # 为所有虚拟机配置SSH设置
            config.ssh.username = "root"
            config.ssh.password = "1"
            config.ssh.insert_key = "true"
            end
        end
    end
end
```
