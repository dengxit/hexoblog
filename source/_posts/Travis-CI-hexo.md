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

#### 在github构建hexo项目

* 将本地的 "hexoblog" （整个项目的源码）上传至github
* 在github 构建 gitpages 项目
* 此部分内容可参考 [超详细Hexo+Github博客搭建小白教程](https://zhuanlan.zhihu.com/p/35668237)

#### 部署 Travis CI

* 本地 hexoblog 下建立travis相关文件
````
mkdir .travis
touch .travis.yml
cd .travis
touch ssh_config
````

* Deploy Key
````
cd ～/.ssh
ssh-keygen -t rsa -C "your_email@example.com"
建立的rsa 命名为travis_rsa 以区分
cat travis_rsa.pub
将控制台输出的密钥拷贝至 "github.io" 项目的 Deploy Key中
````

####  安装travis

````
gem install travis

若有报错
ERROR:  While executing gem ... (Gem::FilePermissionError)
    You don't have write permissions for the /Library/Ruby/Gems/2.6.0 directory.
 
cd ~/Library
ll

sudo chown -R your_name:your_name /Library/Ruby/Gems/2.6.0

 
````

####  配置travis
````
travis login --pro 

cd hexoblig/.travis
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





