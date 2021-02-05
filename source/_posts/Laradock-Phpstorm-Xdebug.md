---
title: Laradock-Phpstorm-Xdebug
date: 2021-02-04 15:13:05
tags:
---

##### Laradock Env Setting
确保laradock .env 中workspace和php-fpm的xdebug拓展安装
```
WORKSPACE_INSTALL_XDEBUG=true
PHP_FPM_INSTALL_XDEBUG=true
```
php-fpm 中安装 xdebug 可调试 Http 请求，比如调试浏览器、Postman 发起的请求，workspace 中安装 xdebug 可以调试 CLI 请求，比如 Artisan 命令、PHP 脚本

<!-- more -->

/laradock/php-fpm/xdebug.ini 设置
```
#xdebug.remote_enable=1
xdebug.mode=debug
#xdebug.remote_host=docker.for.mac.localhost
xdebug.client_host=host.docker.internal
#xdebug.remote_port=9000
xdebug.client_port=9000

xdebug.remote_autostart=1

xdebug.remote_connect_back=0

xdebug.remote_handler=dbgp

xdebug.max_nesting_level=250


xdebug.var_display_max_children=-1
xdebug.var_display_max_data=-1
xdebug.var_display_max_depth=-1

xdebug.remote_log="/var/www/xdebug_logs/xdebug_docker.log"

xdebug.idekey=PHPSTORM

```
其中带 # 号的配置适用于 Xdebug2 版本 ，对应下方设置适用于 Xdebug3 版本

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Xdebug-2-3.png" />

详情见[Phpstorm 2020.3 Configure Xdebug](https://www.jetbrains.com/help/phpstorm/2020.3/configuring-xdebug.html#updatingPhpIni)

##### Phpstorm 设置

* 确保 phpstorm 中 docker 连接成功

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-docker.png" />

* 配置 php-fpm

设置cli
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-php-fpm-1.png" />
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-php-fpm-2.png" />
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-php-fpm-3.png" />
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-php-fpm-4.png" />
设置server
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-php-server-2.png" />

* 启动php-fpm下的xdebug

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/php-fpm-xdebug-start.png" />

* 设置xdebug web page

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/set-web-page-debug-0.png" />

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/set-web-page-debug.png" />

* 代码设置断点

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/set-breakpoint.png" />

* phpstorm 中开启监听

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/start-listening.png" />

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/go-web-page-debug.png" />

点击按钮会自动跳转到带有XDEBUG_SESSION_START的网址，将对应参数拷贝到postman url后

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/postman-request.png" />

* postman send request 自动跳转至phpstorm断点处开始调试

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/go-xdebug.png" />










