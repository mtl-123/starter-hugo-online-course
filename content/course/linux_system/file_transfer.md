

# rsync

```bash

scp远程拷贝和cp本地拷贝都没有断点续传功能，rsync拷贝才有断点续传功能

#本地usb文件以断点续传的方式拷贝到本地目录
rsync -avuP  /mnt/a.tar.gz   /opt/a.tar.gz

显示拷贝进度：
43,269,259   12%   35.8MB/s   18:31:30
ls -a 目录下会生成一个.a.tar.gz.oc0cL8的临时文件

#本地文件以断点续传的方式拷贝到远程服务器
rsync -avuP a.tar.gz  root@192.168.1.100:/opt/a.tar.gz

#远程文件以断点续传的方式拷贝到本地
rsync -avuP root@192.168.1.100:/opt/a.tar.gz ./a.tar.gz    


常用参数：
-a: 以archive模式操作，复制目录、符号连接，等价于 -rlptgoD 。
-v: 打印详情
-u: 只进行更新，防止本地新文件被重写，注意两者机器的时钟的同时
-P: 等同于 --partial --progress
--partial: 保留那些因故没有完全传输的文件,以是加快随后的再次传输(即断点续传)
--progress: 在传输过程中显示进度
-r: 对子目录以递归模式处理
-z: 在传输过程中进行压缩
-t, --times: 保留修改时间

rsync断点续传方式拷贝本地文件或远程文件，目录下会生成一个隐藏的临时文件，ls -a 可以看到，每次终端后，下次拷贝还能续传拷贝
 
```
# rsync 优化成脚本方式(未完成)
```bash
#!/bin/bash

# 配置文件路径
config_file="rsync_config.txt"

# 设置默认值
declare -A config
config=(
  ["source_directory"]=""
  ["target_ip"]=""
  ["destination_directory"]=""
  ["bwlimit"]="1000"
  ["log_file"]="rsync.log"
  ["resume"]=""
  ["user"]=""
  ["password"]="" 
  ["port"]=""
  ["parallel"]=""
)

# 创建配置文件（如果不存在）
if [ ! -f "$config_file" ]; then
  cat <<EOL > "$config_file"
# rsync_config.txt
# 请在此配置文件中填写您的默认值
EOL
  for key in "${!config[@]}"; do
    echo "$key=\"${config[$key]}\"" >> "$config_file"
  done
  echo "已创建默认配置文件：$config_file"
fi

# 加载配置文件
source "$config_file"

# 帮助提示函数
show_help() {
  echo "用法: $(basename "$0") [选项]"
  echo "选项:"
  echo "  -s <源目录>        指定源目录 (默认: ${config['source_directory']})"
  echo "  -i <目标 IP>        指定目标 IP 地址 (必需)"
  echo "  -d <目标目录>        指定目标目录 (默认: ${config['destination_directory']})"
  echo "  -b <传输速度>        指定传输速度限制 (默认: ${config['bwlimit']} KB/s)"
  echo "  -l <日志文件>        指定日志文件 (默认: ${config['log_file']})"
  echo "  -r                启用断点续传"
  echo "  -u <用户名>        指定用户名 (默认: ${config['user']})"
  echo "  -p <密码>          指定密码 (如果未指定密码，则使用SSH密钥认证)"
  echo "  -P <端口号>        指定端口号 (默认: ${config['port']})"
  echo "  --parallel <并行数>   启用并行传输 (默认: ${config['parallel']})"
  echo "  --compress        启用 SSH 压缩"
  echo "  -h                显示此帮助信息"
  exit 1
}

# 处理命令行参数
while getopts "s:i:d:b:l:ru:p:P:h" opt; do
  case $opt in
    s)
      config["source_directory"]="$OPTARG"
      ;;
    i)
      config["target_ip"]="$OPTARG"
      ;;
    d)
      config["destination_directory"]="$OPTARG"
      ;;
    b)
      config["bwlimit"]="$OPTARG"
      ;;
    l)
      config["log_file"]="$OPTARG"
      ;;
    r)
      config["resume"]="--partial"
      ;;
    u)
      config["user"]="$OPTARG"
      ;;
    p)
      config["password"]="$OPTARG"
      ;;
    P)
      config["port"]="-e 'ssh -p $OPTARG'"
      ;;
    h)
      show_help
      ;;
  esac
done

# 处理额外的选项
while [[ $# -gt 0 ]]; do
  case "$1" in
    --parallel)
      config["parallel"]="--parallel=$2"
      shift 2
      ;;
    --compress)
      config["port"]="${config['port']} -C"
      shift
      ;;
    *)
      echo "未知选项: $1"
      show_help
      ;;
  esac
done

# 验证输入
if [ -z "${config['target_ip']}" ]; then
  echo "未提供目标 IP 地址。请使用 -i 选项指定目标 IP 地址。"
  show_help
fi

# 保存配置到配置文件
cat <<EOL >"$config_file"
# rsync_config.txt
# 请在此配置文件中填写您的默认值
EOL

for key in "${!config[@]}"; do
  echo "$key=\"${config[$key]}\"" >> "$config_file"
done

# 提示用户确认设置
echo "以下是配置参数："
for key in "${!config[@]}"; do
  echo "$key: ${config[$key]}"
done

read -p "请确认以上设置是否正确 (y/n): " confirm

if [ "$confirm" != "y" ]; then
  echo "用户取消操作。"
  exit 0
fi

# 启动 rsync 并获取进度信息
(
  if [ -n "${config["password"]}" ]; then
    # 使用密码认证
    rsync ${config["port"]} ${config["parallel"]} ${config["resume"]} --info=progress2 --bwlimit="${config["bwlimit"]}" --log-file="${config["log_file"]}" -avz --password-file=<(echo "${config["password"]}") "${config["source_directory"]}" "${config["user"]}@${config["target_ip"]}:${config["destination_directory"]}" | \
    while IFS= read -r line; do
      if [[ $line == *%* ]]; then
        estimate_time=$(echo "$line" | grep -oE '[0-9]+:[0-9]+:[0-9]+')
        echo "已传输: $line，估计剩余时间: $estimate_time"
      fi
    done
  else
    # 使用SSH密钥认证
    rsync ${config["port"]} ${config["parallel"]} ${config["resume"]} --info=progress2 --bwlimit="${config["bwlimit"]}" --log-file="${config["log_file"]}" -avz -e ssh "${config["source_directory"]}" "${config["user"]}@${config["target_ip"]}:${config["destination_directory"]}" | \
    while IFS= read -r line; do
      if [[ $line == *%* ]]; then
        estimate_time=$(echo "$line" | grep -oE '[0-9]+:[0-9]+:[0-9]+')
        echo "已传输: $line，估计剩余时间: $estimate_time"
      fi
    done
  fi
) &

# 等待 rsync 完成
wait

# 打印完成消息
echo "rsync 同步完成。"

```
