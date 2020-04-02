---
title: Git
date: 2020-03-25 18:13:52
tags: 
 - Git
---


##### Git教程
* [liaoxuefeng](https://www.liaoxuefeng.com/wiki/896043488029600)
* [git-book](https://git-scm.com/book/zh/v2)

##### Git流程简记
```
1.新建issue
2.根据issue号在本地基于master切issue-XXXX分支:git checkout -b issue-XXXX
3.开发&本地测试
4.git status
5.git add .
6.git commit -m "add: XXXXXXXX. close #XXXX"(add,fix,opt... #后的XXXX对应issue号)
7.git push origin issue-XXXX
8.提pr，跑ci，申请review，不通过接着改，循环3->7
9.通过，被merge，issue关闭；
10.出现bug，重开关闭的issue，基于master切bug分支:git checkout -b issue-XXXX-1(无bug则跳过10，11两步)
11.循环3-6，git push origin issue-XXXX-1
12.git checkout master
13.git pull
14.循环1-9开发新功能
```
<!-- more -->
##### Github初始化设置
* 生成 ssh key 
ssh-keygen -t rsa -C "dengxit@gmail.com"
* 将sshkey添加到github设置中
* 测试连接
ssh -T git@github.com
ssh-add ~/.ssh/github_rsa （链接失败执行）
* github新建一个项目
* 本地新建一个项目
 git init
 git remote add github XXXXXXXX/XXX.git
 git add .
 git push origin master
 
##### cherry_pick
 ```
 #在当前分支将某个分支上特定的commit更改拉到当前分支
 git cherry-pick commit-sha1
 ```
 
##### 撤销 最后 一次错误的commit
* 有多个个commit如下
```
commit4
commit3
commit2
commit1
```
* 如果commit4 已经提交 ，想要撤销，执行如下操作
```
git reset --hard HEAD~1
git push --force
```
* 这样commit4就被撤销了。不过如果commit4后有commit5提交，在强制推送后commit5也会消失


##### 测试环境冲突
示例
```
pro          --->    branch-1
branch -1    --->    staging   (conflicts)    branch-1  ----> pro (no-conflicts)
解决：
branch -1    --->    branch -2
branch -2    merge   staging
branch -2    push    staging
这样branch -2 成了一个受污染分支，但是解决了到staging的冲突
```

##### 修改提交者用户名和邮箱
当我们有多个不同的代码仓库时，不同仓库信息验证不同。比如gitlab和github所验证的git 配置信息不同，若没有配置好则会出现配置信息未更改而被拒绝push
```
//查看全局git配置信息
git config --global --list 
// 设置全局git使用的账号信息
git config --global user.name "Author Name"
git config --global user.email "Author Email"
 
//查看当前项目git配置信息
 git config --list 
// 设置当前项目库配置账号信息
git config user.name "Author Name"
git config user.email "Author Email"

// 设置本次commit的提交账号信息
git commit --amend --author="dengxit <dengxit@gmail.com>"

```

##### 批量删除本地分支
```
git branch | grep  "fix" | xargs git branch -d #删除本地名称带 fix 的分支
git branch | grep  "feature" | xargs git branch -d #删除本地名称带 feature 的分支
```

详细内容[参考](https://juejin.im/post/5c3b013a5188252580051517)

##### 更新Git

* [官网下载](https://git-scm.com/download)最新Git

* Mac brew
```
# 替换成阿里巴巴的 brew.git 仓库地址:
cd "$(brew --repo)"
git remote set-url origin https://mirrors.aliyun.com/homebrew/brew.git
 
 
# 替换成阿里巴巴的 homebrew-core.git 仓库地址:
cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core"
git remote set-url origin https://mirrors.aliyun.com/homebrew/homebrew-core.git

brew upgrade git
```
* apt-get
```
add-apt-repository ppa:git-core/ppa
apt-get update
apt-get install -y git
```



