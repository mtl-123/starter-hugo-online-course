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