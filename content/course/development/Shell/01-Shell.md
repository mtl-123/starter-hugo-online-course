---
title: Shell 开发
date: "2021-01-01"
type: book
weight: 40
math: true
tags:
  - Statistics
---

第三章 Shell 脚本入门

<!--more-->

## 脚本格式

脚本以 `#!/bin/bash` 开头 (指定解释器)

```bash
tee >> hell.sh << 'EOF'
#!/bin/bash
echo "hell word dahaihaige"
EOF

# 使用bash 执行hell.sh 脚本
bash hell.sh
```

## 多命令处理

- 需求:
  在/home/user/目录下创建一个 banzhang.txt,在 banzhang.txt 文件中增加"I love csl"
```bash
tee >> banzhang.txt
#!/bin/bash

cd /home/user/

touch banzhang.txt

echo " I love cls"

EOF
```

## 查询Linux上所有开启的端口号

` sudo lsof -i -P -n | grep LISTEN `

该命令将会列出系统上所有正在监听的端口号及其对应的进程名和PID。其中，-i 参数表示只显示与网络相关的文件，-P 参数表示直接使用端口号，而非将端口号转换为服务名称，-n 参数表示不将IP地址转换为主机名。通过 grep LISTEN 来过滤出所有正在监听的端口号，即处于 LISTEN 状态的端口。


## 创建软连接
以绝对路径创建软连接

`ln -s /home/bin/docker-compose   /usr/local/bin/docker-compsoe`

## 安全删除创建的软连接

`unlink file_name`

注意: 需要在创建生成的目录下去安全删除软链链接创建的目录颜色都不一样的，记住不要使用rm 的方式，太不安全了
