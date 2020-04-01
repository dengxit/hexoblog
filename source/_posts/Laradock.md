---
title: Laradock（Docker+Laravel）
date: 2020-04-01 18:34:27
tags:
 - Docker
 - Laravel
---

##### Docker

* [Docker官网](https://www.docker.com/)

* [Docker Mac 下载](http://download.docker.com/mac/stable/Docker.dmg)

<!-- more -->

* 添加Docker阿里云镜像`https://5srh0d9d.mirror.aliyuncs.com`，重启Docker 

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/docker-desktop.png" width = 256 />


##### SSH KEY
```
cd ～/.ssh
ssh-keygen -t rsa -C "your_email@example.com"
#建立的rsa 命名为docker_rsa 以区分
cat docker_rsa.pub
# 将公钥设置到你的Gitlab或者Github仓库
# 将私钥公钥拷贝至后续clone下来的Laradock 的workspace目录下并且更名为insecure_id_rsa和insecure_id_rsa.pub

vi ~/.ssh/config
#加入
host xxx.com  #git服务商网址  
    identityFile ~/.ssh/docker_rsa
    user dengxit@gmail.com #git账号用户名
```
##### Laradock

* [Laradock 官网](http://laradock.io/)

* `git clone https://github.com/Laradock/laradock.git`

* clone下来的项目内有非常多的相关环境目录，只保留Laravel运行环境必要的即可

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/docker-dev.png" width = 256 />

##### Laradock env配置

[官方ENV](https://github.com/laradock/laradock/blob/master/env-example)


保留我们留下来的相关配置即可，根据自己本地或服务器情况进行相应设置，详情可参考[Laradock文档](https://laradock.io/documentation/)

注意

* 将“APP_CODE_PATH_HOST=~/GIT”的GIT改为本地项目所在地址或者上一层目录，本地的所有以Docker运行的项目放在次目录下即可

* 设置镜像 
  WORKSPACE_COMPOSER_REPO_PACKAGIST=https://mirrors.aliyun.com/composer/
  WORKSPACE_NPM_REGISTRY=https://registry.npm.taobao.org
  WORKSPACE_NVM_NODEJS_ORG_MIRROR=https://npm.taobao.org/mirrors/node


##### docker-compose 

* 配置项目的docker-compose.yaml文件 （同env 保留我们留下来的目录配置即可）
```
docker-compose build #build镜像
docker-compose up -d #start
docker ps  #查看build后启动的镜像
```

##### Laravel站点设置

* 在docker项目nginx/sites下新增项目配置
```
cp laravel.conf.example demo.conf
```

*  修改 conf 
```
server_name laravel.test;
root /var/www/laravel项目路径/public;
```

* 本地hosts增加
```
127.0.0.1 laravel.test
```

* Laravel env 设置
```
将项目的DBHOST改为mysql，REDISHOST改为redis，密码改成docker redis中对应密码
```

##### Docker Workspace
通过 ssh root@localhost -p 2222连接到workspace中

```
cd /var/www 
ll  #在容器中查看项目
```

##### 常见问题

```
No containsers to start ，无容器可启动
docker-compose up -d
```


```
Starting nginx            ... error
netstat -an | grep 80查看80端口，关闭本机运行中的nginx
```



```
mysql端口占用
docker中的mysql和本地mysql服务共同占用了3306端口，将env中端口号改为8306
执行docker-compose up -d --force-recreate --no-deps mysql
```

```
composer install 时提示版本问题
将env中的PHP_VERSION=改成需要的版本，rebuild
或者是配置多个workspace 每个workspace中采用不同的php版本
```

##### 相关链接
[compose命令详解](https://www.cnblogs.com/liubin0509/p/6178351.html )

```
 --force-recreate       强制重新创建compose服务，即使没有任何改变。重新创建后启动容器
 --no-deps             不启动link服务，只启动指定的服务.
 ```
 
 
##### 多个workspace配置

当项目变多，并且出现各种不同版本的PHP运行环境时，可以配置多个workspace，每个采用不同的环境设置。
 


