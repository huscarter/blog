---
title: terminal配置代理
date: 2021-11-12 10:29:44
categories: 技术周边
tags: terminal
description: 在墙内搞技术，你需要多学会一门技术：FQ。当我们电脑设置了代理比如v2ray，浏览器访问外网没有问题，但是terminal却依然不能连接外网。下面就来介绍如果配置terminal，使之可以访问外网。
---

### 开启v2ray代理
暂无。

### 修改系统配置参数

```
// 打开系统配置参数文件
vim ~/.zshrc

// 添加如下信息
alias proxy='export all_proxy=socks5://127.0.0.1:1080'
alias unproxy='unset all_proxy'

// 保存之后运行如下命令使配置生效
source ~/.zshrc
```



### 开启关闭代理

```
// 在terminal输入如下命令,开启代理
proxy

// 在terminal输入如下命令,关闭代理
unproxy

// 可以利用命令查看ip服务商信息来确认代理是否成功
curl cip.cc
```



