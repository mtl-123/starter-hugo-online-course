---
title: SSH公钥批量下发
date: '2023-03-10'
type: book
weight: 2
---

# ssh 公钥批量分发



```bash

#!/bin/bash
# Author:
# 公钥分发脚本
# time: 2023-03-10

# 定义需要分发密钥的地址段
host_pre=10.0.0.
# 定义端口号
port=22
# 定义远程主机密码
password=vagrant

. /etc/init.d/functions
if [ ! -f ~/.ssh/id_rsa.pub ] ;then

ssh-keygen -f ~/.ssh/id_rsa -N ''|echo ''
fi

# 循环遍历
for ip in {100..105}
do
  echo  "===================== 主机 $host_pre$ip 开始分发公钥   ==================== "
  sshpass -p$password ssh-copy-id -i ~/.ssh/id_rsa.pub vagrant@$host_pre$ip -p $port "-o StrictHostKeyChecking=no" &>/dev/null
  if [ $? -eq 0 ] ; then
    action "主机 $host_pre$ip 分配" /bin/true
    echo ""
  else
    action "主机 $host_pre$ip 分配" /bin/false
    echo "==================== 主机 $host_pre$ip 已分发完公钥   ==================== "
    echo ""
  fi
done

```
## 创建用户，删除用户脚本
```
#!/bin/bash

# 函数：提示管理员选择操作
prompt_option() {
    echo "请选择要执行的操作："
    echo "1. 创建用户"
    echo "2. 删除用户"
    read -p "请输入选项: " option
}

# 函数：创建用户
create_user() {
    read -p "请输入要创建的用户名： " username

    # 检查用户名是否已存在
    if id "$username" &>/dev/null; then
        echo "用户名已存在，请选择其他用户名。"
        return 1
    fi

    # 提示管理员输入用户密码并验证
    while true; do
        read -s -p "请输入用户 $username 的密码： " password
        echo
        read -s -p "请再次输入密码以确认： " password_confirm
        echo

        if [[ "$password" != "$password_confirm" ]]; then
            echo "两次输入的密码不一致，请重新输入。"
        else
            break
        fi
    done

    # 创建用户并设置密码
    sudo useradd -m -s "$shell" "$username"
    echo "$username:$password" | sudo chpasswd

    # 设置默认Shell
    sudo chsh -s "$shell" "$username"

    # 根据包管理器类型，添加用户到相应的组
    if [[ "$pkg_manager" != "" ]]; then
        sudo usermod -aG "${pkg_groups[$pkg_manager]}" "$username"
    fi

    # 打印创建成功的消息
    echo "用户 $username 创建成功！"

    # 记录创建日志
    logger "创建用户 $username"

    # 用户自定义选项
    read -p "是否设置SSH公钥？(y/n)：" ssh_option
    if [[ "$ssh_option" == "y" ]]; then
        read -p "请输入SSH公钥路径： " ssh_key_path
        sudo mkdir -p "/home/$username/.ssh"
        sudo tee "/home/$username/.ssh/authorized_keys" < "$ssh_key_path" >/dev/null
        sudo chown -R $username:$username "/home/$username/.ssh"
        sudo chmod 700 "/home/$username/.ssh"
        sudo chmod 600 "/home/$username/.ssh/authorized_keys"
    fi

    # 其他用户配置
    # ...
}

# 函数：删除用户
delete_user() {
    read -p "请输入要删除的用户名： " username

    # 检查用户是否存在
    if ! id "$username" &>/dev/null; then
        echo "用户不存在，请选择其他用户。"
        return 1
    fi

    # 删除用户及其主目录
    sudo userdel -r "$username"

    # 打印删除成功的消息
    echo "用户 $username 删除成功！"
}

# 主脚本

# 设置默认Shell和包管理器相关信息
shell="/bin/bash"
pkg_groups=("apt-get:sudo" "yum:wheel")

# 提示管理员选择操作
prompt_option

if [[ "$option" == "1" ]]; then
    create_user
elif [[ "$option" == "2" ]]; then
    delete_user
else
    echo "无效的选项，请重新运行脚本并选择正确的选项。"
fi
```
