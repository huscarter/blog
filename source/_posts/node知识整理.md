---
title: node知识整理
date: 2024-01-28 13:01:01
categories: web
tags:
     - web
description: node知识整理。
---

## node简介

## node安装
### node管理工具
1. n
```
// 安装n
brew install n
// 查看n版本
n --version
// n 安装 node
n 18.14.2
// n 删除 node 版本
n rm 18.14.2
// n 查看已安装的node
n [ls/list]
// n 切换 node 版本
n
// 查看当前 node 版本
node --version
```

2. nvm
```
// 安装nvm
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.8/install.sh | bash
// 查看nvm版本
nvm -v
// nvm 安装 node
nvm install 18.14.2
// nvm 删除 node 版本
nvm uninstall 18.14.2
// nvm 切换 node 版本
nvm use 4
// nvm 设置默认的node版本
nvm alias default 0.12.7 
// nvm 查看已安装的node
nvm list
```
