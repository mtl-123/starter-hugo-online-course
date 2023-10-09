---
title: Git 使用
date: '2022-10-19'
type: book
weight: 40
math: true
tags:
  - current
---



windows系统 git add . 提交时报错

解决方法: `git config --global core.autocrlf false` 就不会报错了

## 提交代码到远程GitHub仓库
```bash
git init
git add README.md
git commit -m "first commit"
git branch -M master
git remote add origin https://github.com/mtl-123/laochen_blog.git
git push -u origin master
```
