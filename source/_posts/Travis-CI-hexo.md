---
title: Travis-CI+Hexo
date: 2020-03-25 01:07:11
tags:
 - 教程
---

#### Travis-CI Hexo？
* [What Is Continuous Integration (CI)](https://docs.travis-ci.com/user/for-beginners/)
* 持续集成是一种经常合并小的代码更改的实践，通过以较小的增量开发和测试来构建更合理的软件开发流程，而Travis CI 则是一个持续集成测试工具
* Hexo 是一个快速、简洁且高效的博客框架，也是本博客所使用的框架
* [Hexo Docs](https://hexo.io/zh-cn/docs/configuration)

<!-- more -->
#### 在github构建hexo项目

* 将本地的 "hexoblog" （整个项目的源码）上传至github
* 在github 构建 gitpages 项目
* 此部分内容可参考 [超详细Hexo+Github博客搭建小白教程](https://zhuanlan.zhihu.com/p/35668237)

#### 部署 Travis CI

* 建立travis相关文件
````
cd hexoblog #本地 hexoblog 下
mkdir .travis
touch .travis.yml
cd .travis
touch ssh_config
````
![](http://q7t579i7q.bkt.clouddn.com/Travis.png)

#### Deploy Key
````
cd ～/.ssh
ssh-keygen -t rsa -C "your_email@example.com"
#建立的rsa 命名为travis_rsa 以区分
cat travis_rsa.pub
````
将控制台输出的密钥拷贝至 "github.io" 项目的 Deploy Key中
勾选Allow write access
![](http://q7t579i7q.bkt.clouddn.com/DeployKey.png)

####  安装Travis

````
gem install travis

若有报错
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
 
cd ~/Library
ll

sudo chown -R your_name:your_name /Library/Ruby/Gems/2.6.0

 
````

####  配置Travis
使用github 账号授权登陆 travis-ci.com
![](http://q7t579i7q.bkt.clouddn.com/Sign-up-travis.png)
````
travis login --pro 

cd hexoblog/.travis
travis encrypt-file ~/.ssh/travis_rsa --add --com
#生成了$encrypted_xxxxxxda_key 和 $encrypted_xxxxxxda_iv
vi ssh_config
#加入
Host github.com
  User dengxit@gmail.com
  StrictHostKeyChecking no
  IdentityFile ~/.ssh/travis_rsa
  IdentitiesOnly yes

 
````

####  配置travis.yml
````
language: node_js

node_js:
  - stable

before_install:
  # Decrypt the private key
  - openssl aes-256-cbc -K $encrypted_xxxxxxda_key -iv $encrypted_xxxxxxda_iv
      -in .travis/travis_rsa.enc -out ~/.ssh/travis_rsa -d
  # Set the permission of the key
  - chmod 600 ~/.ssh/travis_rsa
  # Start SSH agent
  - eval $(ssh-agent)
  # Add the private key to the system
  - ssh-add ~/.ssh/travis_rsa
  # Copy SSH config
  - cp .travis/ssh_config ~/.ssh/config
  # Set Git config
  - git config --global user.name "dengxit"
  - git config --global user.email dengxit@gmail.com
  # Install Hexo
  - npm install hexo-cli -g
  # Clone the repository
  - git clone https://github.com/dengxit/dengxit.github.io .deploy

script:
  - hexo generate
  - hexo deploy

branches:
  only:
    - master
````
#### 项目更新Push成功Build成功
![](http://q7t579i7q.bkt.clouddn.com/build-success.png)

#### 部署过程中的深坑

*  Travis CI构建一直提示 iv undefined
````
travis 有两个站点 travis-ci.org 和 travis-ci.com
travis login --pro   #travis-ci.com --auto 是 org
travis encrypt-file ~/.ssh/travis_rsa --add --com # --com 代表 travis-ci.com
#注意不要弄混了，在执行命令后查看travis 项目设置中是否自动添加了对应的encrypted_xxxxxxda_key 和 encrypted_xxxxxxda_iv

````
*  Travis CI自动构建部署之后，博客页面空白

````
#原因是使用了next主题配置
#删除themes/next的.git和.gitignore,执行下方命令
cd hexoblog
git rm --cached themes/next
git add themes/next
git push origin master

````

#### 参考文章

[Github集成TravisCI：自动发布](https://github.com/levy9527/blog/issues/1)
[Travis自动部署网站](https://zespia.tw/blog/2015/01/21/continuous-deployment-to-github-with-travis/)
[使用Travis CI自动构建Hexo静态博客](https://researchlab.github.io/2016/05/08/travis-ci-deploy-hexo-blog/)
[使用 Travis 自动化部署 Hexo Blog](https://g2ex.top/2019/06/28/hexo-with-travis-ci/)
[Mac OS X 下使用 Ruby Gem 的两个坑](https://www.jianshu.com/p/bb9fe3fd45d0)


#### Hexo 主题优化

[Next主题优化](https://www.jianshu.com/p/3ff20be8574c)





