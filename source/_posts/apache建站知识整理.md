---
title: Apache建站知识梳理
date: 2021-11-23 20:29:44
categories: web
tags:
    - 建站
description: 最近帮了亲戚做了2个简单的公司网站，了解了一些建站相关的知识。于是整理成挡，方便后期查阅。
---

## 前期准备

#### 云服务器选择

我们的网站想让别人访问是需要放到公网服务器上的，目前最方便的做法就是购买云服务器。下面简单整理了国内主要的云服务器供应商。

1. [阿里云](https://homenew.console.aliyun.com/home/dashboard/ProductAndService)

2. [腾讯云](https://cloud.tencent.com/)

3. [ucloud](https://www.ucloud.cn/)

我自己之前买的是阿里云服务器，ucloud新出之后因为价格优势，购买了ucloud服务器；腾讯云我还没购买过。阿里云和ucloud个服务器用起来没有太大区别，但是如果你是要使用v2ray做中转站用，建议使用ucloud；阿里云之前是不是就渠道断联。

####  http服务器软件选择

购买了云服务器之后，需要在云服务器上安装http服务器软件，到时候我们的网站文件就是放到http服务器指定的目录里。主要的http服务器有IIS、Apache、Tomcat、Weblogic、WebSphere，我使用的是apache2，下面我们都以apache2为例子。

## 建站

#### 安装apache

1. 登录到云服务器

   ```
   // 通过用户名name连接服务器xxx.xxx.xxx.xxx
   ssh name@xxx.xxx.xxx.xxx
   // 之后输入密码
   ```

2. 通过yum安装apache

   ```
   // apache服务器的软件包名为httpd
   yum install httpd
   ```

3. 启用apache

   ```
   // 启用服务器之后可通过浏览器访问xxx.xxx.xxx.xxx试试
   start httpd
   enable httpd
   ```

#### 配置虚拟站点

```
// 打开配置文件
vim /etc/apache2/apache2.conf

// 按照如下配置修改文件内容

# xxx1.com域名对应的站点目录
<VirtualHost *:80>
ServerName xxx1.com
DocumentRoot /var/www/html1
</VirtualHost>

# www.xxx1.com域名对应的站点目录
<VirtualHost *:80>
ServerName www.xxx1.com
DocumentRoot /var/www/html1
</VirtualHost>

# xxx2.com域名对应的站点目录
<VirtualHost *:80>
ServerName xxx2.com
DocumentRoot /var/www/html2
</VirtualHost>

# www.xxx2.com域名对应的站点目录
<VirtualHost *:80>
ServerName www.xxx2.com
DocumentRoot /var/www/html2
</VirtualHost>
```

#### 传输网站文件到服务器

```
// 将网站文件传到云服务器
scp xxx.zip name@xxx.xxx.xxx.xx:/home/name/download/
// 解压
unzip xxx.zip
// 将网站文件放到http目录
cp -r xxx/* /var/www/html1/
```



