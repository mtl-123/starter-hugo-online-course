---
title: Jenkins 安装
date: '2022-10-19'
type: book
weight: 20
---

```bash

#!/bin/bash

# 安装相关依赖
sudo yum install -y curl policycoreutils-python openssh-server perl postfix  wget 

# # 启动ssh服务&设置为开机启动
# systemctl enable postfix && systemctl  start postfix

# # 设置postfix开机自启，并启动，postfix支持gitlab发信功能

# # 开放ssh以及http服务，然后重新加载防火墙列表
# sudo firewall-cmd --permanent --add-service=http
# sudo firewall-cmd --permanent --add-service=https
# sudo systemctl reload firewalld
# # 下载gitlab包，并安装

# sudo systemctl enable sshd
# sudo systemctl start sshd


# rpm包 下载链接https://packages.gitlab.com/gitlab/gitlab-ce?page=146
wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-12.4.2-ce.0.el7.x86_64.rpm/download.rpm

# 安装
rpm -i gitlab-ce-12.4.2-ce.0.el7.x86_64.rpm

# 修改配置文件

[root@m1 gitlab]# ls
gitlab.rb  gitlab-secrets.json  trusted-certs
[root@m1 gitlab]# vim gitlab.rb 

# 修改外部访问都url地址，我这里是当前gitlab的服务器ip地址+82端口号
# 使用vim 编辑器搜索 :?external_url 就直接定位过去了

## GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
external_url 'http://192.168.56.100:82'



# 修改nginx监听端口号
# vim编辑器中搜索 :?nginx['listen_port']

##! **Override only if you use a reverse proxy**
##! Docs: https://docs.gitlab.com/omnibus/settings/nginx.html#setting-the-nginx-listen-port
nginx['listen_port'] = 82
# :wq! (保存并推出)

# 重新加载修改过的配置文件
[root@m1 gitlab]# gitlab-ctl reconfigure
Starting Chef Client, version 14.13.11
resolving cookbooks for run list: ["gitlab"]
Synchronizing Cookbooks:
........
Running handlers:
Running handlers complete
Chef Client finished, 16/707 resources updated in 56 seconds
gitlab Reconfigured! （GitLab重新配置！）

# 重启gitlab服务
[root@m1 gitlab]# gitlab-ctl restart
ok: run: alertmanager: (pid 31899) 1s
ok: run: gitaly: (pid 31923) 0s
ok: run: gitlab-exporter: (pid 31945) 0s
ok: run: gitlab-workhorse: (pid 31956) 1s

# 把端口添加到 防火墙
firewall-cmd  --zone=public  --add-port=82/tcp  --permanent
firewall-cmd  --reload

# 浏览器访问ip+83端口

# 重置密码

# 登录
192.168.56.100:82
```