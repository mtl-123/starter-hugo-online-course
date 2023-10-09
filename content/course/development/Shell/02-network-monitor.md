---
title: 网络监控脚本
date: "2023-05-10"
type: book
weight: 40
math: true
tags:
  - Statistics
---


vim network_monitor.sh
```bash
#!/bin/bash

# variables
lock="/tmp/$(basename "$0").lock"
cache_file="/tmp/speedtest_cache.txt"
server_id=258
timeout=15
source_ip=""

# check if lock file exists
if [ -f "$lock" ]; then
  echo "锁文件 ${lock} 存在，该脚本正在运行中。"
  exit 1
fi

# create lockfile and cleanup on exit
trap 'rm -f "$lock"' EXIT
touch "$lock"

# Define a function that pings the specified IP address and returns packet loss and avg delay.
ping_ip() {
  pingtest=$(ping -c "$2" -q "$1")
  packet_loss=$(echo "$pingtest" | awk -F'[, ]+' '/^rtt/ {print $6}' | cut -d% -f1)
  avg_delay=$(echo "$pingtest" | awk -F'[/ ]+' 'END {print $5}')
  echo "$packet_loss $avg_delay"
}

# Define a function that tests the specified IP address bandwidth and returns upload and download speeds.
speedtest_ip() {
  # check if cache file exists and is less than 5 minutes old
  if [[ -f "$cache_file" && "$(find "$cache_file" -mmin +5)" != "" ]]; then
    cat "$cache_file"
  else
    speedtest_cli --quiet --json --server-id="$server_id" --timeout="$timeout" --source-ip "$source_ip" > "$cache_file"
    cat "$cache_file"
  fi
}

while true; do
  # current timestamp
  current_time=$(date "+%Y-%m-%d %H:%M:%S")
  
  # Call the ping_ip() and speedtest_ip() functions with IP and count parameters.
  results=($(ping_ip "$1" "$2"))
  
  # calculate upload and download speed by reusing cached output if available
  if [[ -f "$cache_file" && "$(find "$cache_file" -mmin +5)" != "" ]]; then
    upload_speed=$(cat "$cache_file" | jq -r '.upload/1024/1024' | bc -l | xargs printf "%.2f")
    download_speed=$(cat "$cache_file" | jq -r '.download/1024/1024' | bc -l | xargs printf "%.2f")
  else
    speedtest_output=$(speedtest_ip)
    upload_speed=$(echo "$speedtest_output" | jq -r '.upload/1024/1024' | bc -l | xargs printf "%.2f")
    download_speed=$(echo "$speedtest_output" | jq -r '.download/1024/1024' | bc -l | xargs printf "%.2f")
  fi
  
  # Output the results with color-coded text.
  if [ "${results[0]}" -eq 0 ]; then
    status_code="\e[32m"
  elif [ "${results[0]}" -le 5 ]; then
    status_code="\e[33m"
  else
    status_code="\e[31m"
  fi
  
  # use tput to format output colors
  tput setaf 2
  echo "[$current_time] IP地址: $1 丢包率: ${results[0]}% 平均延迟: ${results[1]} ms 带宽：上行 ${upload_speed} Mb/s，下行 ${download_speed} Mb/s"
  tput sgr0
  
  sleep 1
done
```
## 使用方法
` sh network-monitor.sh  10.0.0.11 3 ` 
## 此脚本功能

这个脚本是一个 Bash 脚本，主要用于测试 IP 地址的响应时间和带宽。具体功能如下：

1. 检查是否存在锁文件，并且如果该文件已经存在则退出脚本。
2. 创建一个名为 lock 的锁文件，并在退出时自动删除。
3. 定义 ping_ip() 函数，用于测试指定 IP 地址的丢包率和平均延迟，其返回值为一个包含丢包率和平均延迟的数组。
4. 定义 speedtest_ip() 函数，用于测试指定 IP 地址的带宽并返回上传和下载速度。
5. 进入无限循环，并在循环中使用 ping_ip() 和 speedtest_ip() 函数来测试指定 IP 的丢包率、平均延迟和带宽。
6. 根据测试结果输出彩色文本。如果丢包率为 0%，则输出绿色文本，小于等于 5% 输出黄色文本，否则输出红色文本。
7. 在输出结果后休眠 1 秒。
