---
title: Shell脚本安装Docker
date: '2022-10-16'
type: book
weight: 40
math: true
tags:
  - Statistics
---

{{< toc hide_on="x1" >}}

```bash
tee > apt_install_docker.sh <EOF
#!/bin/bash
# install docker
read -p "Install docker $dockerVersion [y/N]? "

if [[ $REPLY =~ ^[Yy]$ ]]; then
# 删除旧版本
sudo apt-get remove docker docker-engine docker.io containerd runc -y
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common  \
    openssl  -y
# 添加阿里云存储库
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -

sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"

sudo apt-get update

# 这里需要修改成变量（后期优化）
sudo apt-get install docker-ce=5:19.03.8~3-0~ubuntu-bionic docker-ce-cli=5:19.03.8~3-0~ubuntu-bionic containerd.io -y

# fi
# # install nvidia docker
# read -p "Install nvidia docker [y/N]? "
# if [[ $REPLY =~ ^[Yy]$ ]]; then
# distribution=$(
#     . /etc/os-release
#     echo "$ID""$VERSION_ID"
# )

# curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
# curl -s -L https://nvidia.github.io/nvidia-docker/"$distribution"/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

# sudo apt-get update && sudo apt-get install -y nvidia-docker2

cat <<EOF >/etc/docker/daemon.json
{
    "default-runtime": "nvidia",
    "runtimes": {
        "nvidia": {
            "path": "/usr/bin/nvidia-container-runtime",
            "runtimeArgs": []
    }
},
    "exec-opts":["native.cgroupdriver=systemd"],
    "registry-mirrors":
[
    "https://hub-mirror.c.163.com",
    "https://mirror.baidubce.com"
]
}
EOF
sudo usermod -aG docker $(whoami)
newgrp docker
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl enable docker
fi
EOF
# 设置可执行权限
sudo chomod +x apt_install_docker.sh
# 开始安装
sudo ./apt_install_docker.sh

```
