---
widget: portfolio
headless: true
weight: 30
title: 探索高级课程
subtitle:
content:
  filters:
    folders:
      - course
    kinds:
      - section
    exclude_tags:
      - preface

  filter_default: 0

  filter_button:
    - name: 所有课程
      tag: '*'
    - name: Kubernetes
      tag: Kubernetes
    - name: 虚拟化
      tag: virtualization
    - name: 运维
      tag: current
    - name: 开发
      tag: develop
    - name: 数据库
      tag: database

design:
  columns: '1'
  view: masonry
  flip_alt_rows: false
---
