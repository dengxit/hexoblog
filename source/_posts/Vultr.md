---
title: 使用Vultr搭建Vpn
date: 2019-12-01 14:51:14
tags:
 - 教程
---
#### 注册Vultr
* [Vultr](https://www.vultr.com/?ref=8334405)
#### 购买服务器
* Cloud compute
* Tokyo
* Centos 7x64
* $5/mo

<!-- more -->
#### 部署 Deploy Now

#### 使用vultr搭建ss服务器
* ssh登陆服务器
* 安装shadowsocks
```
wget –no-check-certificate -O shadowsocks.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks.sh
```
* 获取 shadowsocks.sh 读取权限
```
chmod +x shadowsocks.sh
```
* 设置密码和端口
```
./shadowsocks.sh 2>&1 | tee shadowsocks.log
```
* 使用aes-256-cfb加密模式
* 之后就可以看到Vpn信息

#### 使用Shadowsocks
* [Shadowsocks Mac os](https://github.com/shadowsocks/ShadowsocksX-NG/releases)

* shadowsocks添加服务器设置


* [文章参考](https://wistbean.github.io/vultr-vps-bbr-ss.html)


