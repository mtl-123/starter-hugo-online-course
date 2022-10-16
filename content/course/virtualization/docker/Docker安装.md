---
title: Docker的安装
date: '2022-10-16'
type: book
weight: 40
math: true
tags:
  - Statistics
---

{{< toc hide_on="x1" >}}

### ubuntu系统
### apt安装
```bash
# 1. 设置存储库
sudo apt-get install \
  ca-certificates \
  curl \
  gnupg \
  lsb-release -y

# 2. 添加Docker的官方GPG密钥
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

# 3. 设置存储库
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# 4. 安装Docker引擎
sudo apt-get update
# 4.1 安装最新版本
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

# ================================安装指定版本的docker================================================
# 4.2 安装指定版本
# 列出docker版本号
apt-cache madison docker-ce 

# 创建变量写入想要安装的版本号
version="19.03.8"
# 查找版本号信息并打印出版本号
vagrant@m1:~$ apt-cache madison docker-ce | grep $version | awk '{print $3 }'
# 这是查找出的结果
5:19.03.8~3-0~ubuntu-bionic
# 再创建一个变量
getversion="5:19.03.8~3-0~ubuntu-bionic"
# 开始安装
sudo apt-get install docker-ce=$getversion docker-ce-cli=$getversion containerd.io -y
# 查看版本号
docker version

```
### 配置daemon.jso
```bash
# 先提权
sudo -i
# 写入信息
cat <<EOF >/etc/docker/daemon.json
{
  "exec-opts":["native.cgroupdriver=systemd"],

  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}
EOF

# 检查否写入成功
cat /etc/docker/daemon.json

```
### 添加到用户组
```bash
# 查看用户组及成员
sudo cat /etc/group | grep docker
# 将docker添加到用户组
sudo usermod -aG docker $USER
newgrp docker

# 加载
sudo systemctl daemon-reload
# 重启
sudo systemctl restart docker
# 设置自启动
sudo systemctl enable docker
```
### 卸载docker
```bash

sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin -y

```
---

### 二进制安装(推荐安装方法)
> 使用二进制安装的好处就是可以跨平台运行

```bash
# 下载二进制文件
# 创建变量
getversion="19.03.8"
# docker x86_64架构下载地址 列表中下载自己需要的版本，我实验中使用的是19.03.8版本
wget https://download.docker.com/linux/static/stable/x86_64/docker-${getversion}.tgz
# 解压
tar -zxvf docker-19.03.8.tgz
# 移动
sudo mv docker/* /usr/local/bin
```
### 添加自启动服务
```bash
# ubuntu系统
sudo vim /lib/systemd/system/docker.service

# centos系统
vim /usr/lib/systemd/system/docker.service
# docker.service 内容如下
[Unit]
Description=Docker Application Container Engine
Documentation=https://docs.docker.com
After=network-online.target firewalld.service
Wants=network-online.target

[Service]
Type=notify
ExecStart=/usr/local/bin/dockerd		# 注意修改解压文件的绝对路径
ExecReload=/bin/kill -s HUP $MAINPID
LimitNOFILE=infinity
LimitNPROC=infinity
TimeoutStartSec=0
Delegate=yes
KillMode=process
Restart=on-failure
StartLimitBurst=3
StartLimitInterval=60s

[Install]
WantedBy=multi-user.target

# 添加开机自启动
sudo systemctl enable docker

```


### 修改默认存储

```bash
sudo vim /etc/docker/daemon.json
{
  "exec-opts":["native.cgroupdriver=systemd"],

  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com",
    #这里是修改的docker粗出路径
    "data-root":"/home/storage"
  ]
}

# 查看修改过的存储路径
sudo docker info | grep "Docker Root Dir:"
```
---

### Docker 降级
> 降级并安装指定docker版本

```bash
# ubuntu系统
# 先查看本机源是否有需要降级指定的版本(如果没有请添加指定版本的docker安装源)
apt-cache madison docker-ce 
# 创建变量
getversion="18.03.1~ce~3-0~ubuntu"
# 开始降级
sudo apt-get --allow-downgrades --allow-change-held-packages install  -y  docker-ce=$getversion

# centos系统

yum downgrade --setopt=obsoletes=0 -y docker-ce-${version} docker-ce-selinux-${version}
```