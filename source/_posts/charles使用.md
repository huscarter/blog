---
title: charles 使用
date: 2018-08-06 20:29:44
categories: 技术周边
tags:
     - 技术周边
description: 做移动开发时，有时想拦截查看app的请求url信息，这个时候charles神器就展现出了它的用武之地。
---

## 安装 charles
1. [下载地址](https://www.charlesproxy.com/download/)下载好之后直接傻瓜式安装
2. 因为Charles不是从AppStore上安装的应用程序，安装和运行都需要修改系统偏好设置。找到系统设置中的安全与隐私，修改为允许以下位置下载的应用为任何来源。

## 抓包
确保Android、iOS设备与运行Charles的mac电脑处于同一个wifi环境下。

### 抓取手机http请求
1. 开启电脑代理功能

   ```
   Charles -> Proxy -> 勾选 enable http transparent proxy
   ```

2. 将macOS proxy关掉（抓电脑端请求的）避免干扰。

3. 将手机的网络设置为电脑代理。

   ```
   3.1 手机 -> 设置 -> wifi（同电脑连接的wifi相同） -> 高级设置 ->代理配置
   // 可通过Charles -> help -> local ip address 查看电脑ip地址
   3.2 输入ip地址
   // 可通过Charles -> Proxy 查看端口号
   3.3 驶入端口号
   ```

3. 如果链接成功Charles会有一个弹框获取用户的许可，允许之后用手机走个网络请求就能看到抓取的信息。

### 抓取手机https请求

<font color="red">代理原理：因为手机端安装charles的证书，所以当charles充当服务器时能和客户端完成ssl认证机制。之后charlse服务器将手机的请求转发到真正的服务器完成请求。</font>

1. 手机端安装charles的证书；通过下面的地址获取证书安装

   可通过Charles -> Help -> SSL Proxying -> Install Charles Root Certificate on Mobile Device or Remote Browser

   ```
   chls.pro/ssl
   ```
   
1. 手机信任证书（iPhone为力）

   <font color="red">注意：这里需要2次信任操作</font>

   ```
   第一步：手机 -> 设置 -> 描述文件 -> 信任
   第二步：手机 -> 设置 -> 关于手机 -> 证书信任设置（最底部） -> 开启信任Charles证书
   ```
   
2. 添加charles抓取的https域名 （可选），不添加软件默认就是抓取所有的请求

   ```
   Charles -> Proxy -> SSL Proxying Settings -> SSL Proxying -> Add // 域名+端口号（443）
   ```
   
3. 走一个https请求。

## 问题

1. charles是收费的，如果试用期到了“每次”只能使用30分钟，之后不能抓包。可以考虑买个。
2. 如果http是乱码需要在配置里配置解码格式，找到安装目录下的info.plist，在vmoption里添加如下代码。
```
<string>-Dfile.encoding=UTF-8</string>
```





