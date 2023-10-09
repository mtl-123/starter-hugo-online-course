---
title: GitLab 
date: '2021-01-01'
type: book
weight: 40
math: true
tags:
  - Statistics
---

## docker-compose 部署GitLab-15.11.4

[gitlab/gitlab-ce:latest 可修改其镜像为 "gitlab/gitlab-ce:15.11.0-ce.0" ](https://www.czerniga.it/2021/11/14/how-to-install-gitlab-using-docker-compose/)

## docker-compose 部署方式二
```yaml
version: '3.6'
services:
    web:
      image: 'gitlab/gitlab-ee:latest'
      container_name: 'gitlab-server'
      restart: always
      hostname: '<b>gitlab.example.com</b>'
      environment:
      GITLAB_OMNIBUS_CONFIG: |
          external_url 'https://<b>DOMAIN_OR_IP</b>'
          gitlab_rails['smtp_enable'] = true
          gitlab_rails['smtp_address'] = "<b>SMTP_SERVER</b>"
          gitlab_rails['smtp_user_name'] = "<b>SMTP_SERVER_USERNAME</b>"
          gitlab_rails['smtp_password'] = "<b>SMTP_SERVER_PASSWORD</b>"
          gitlab_rails['smtp_domain'] = "<b>DOMAIN</b>"
          gitlab_rails['smtp_enable_starttls_auto'] = true
          gitlab_rails['smtp_port'] = 587
          gitlab_rails['smtp_authentication'] = "login"
          gitlab_rails['gitlab_email_from'] = '<b>FROM_EMAIL</b>'
          gitlab_rails['gitlab_email_reply_to'] = '<b>REPLY_EMAIL</b>'
          # Add any other gitlab.rb configuration here, each on its own line
          ports:
          - '80:80'
          - '443:443'
          - '22:22'
          - '587:587'
          volumes:
          - '$GITLAB_HOME/config:/etc/gitlab'
          - '$GITLAB_HOME/logs:/var/log/gitlab'
          - '$GITLAB_HOME/data:/var/opt/gitlab'
      shm_size: '256m'
```

## CentOS 8.5 安装GitLab-ee:15.11.3
```bash
# 1.安装配置必要的依赖
yum install -y curl  policycoreutils-python-utils openssh-server perl

systemctl enable sshd
systemctl start sshd

sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld

sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix

# 添加GitLab包仓库并安装包
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash



# 开始安装
sudo EXTERNAL_URL="https://gitlab.demo.com" yum install -y gitlab-ee


# 在主机hosts中配置域名解析
10.0.0.8 gitlab.demo.com

# 初始化
gitlab-ctl reconfigure

# 浏览器访问
https://gitlab.demo.com

用户名： root
密码： 在 cat /etc/gitlab/initial_root_password 中
```
## 使用git clone 项目
报错如下

![image](https://github.com/mtl-123/starter-hugo-online-course/assets/65467296/1662b254-1d72-4f13-ac33-b5f5bc02c3e6)
解决方法：
`git config --global http.sslVerify false`
再去git clone 就不会报错，成功pull代码了

