---
title: Sentry-Laravel-Gitlab
date: 2020-03-27 13:10:15
tags:
 - 教程
---

##### Sentry

[Sentry](https://sentry.io/welcome/)是一个开放源代码错误跟踪系统. 
GitLab允许管理员将Sentry连接到GitLab，以允许用户查看GitLab中的Sentry错误列表

<!-- more -->
##### Sentry-Login
注册Sentry账号后登陆
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Sentry-User-Set.png"/>


##### Sentry-Create-Laravel-Project

设置 》项目 》创建项目 》Laravel

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Sentry-Create-Project.png" />

##### Sentry-Relate-to-Gitlab

设置 》集成 》Gitlab 》Add Installation 将Sentry关联至Gitlab仓库

文档：[Sentry-Relate-to-Gitlab](https://docs.sentry.io/workflow/integrations/global-integrations/#gitlab)


##### Laravel-Set-Sentry

在laravel 项目中安装 sentry-laravel 包
文档：[Laravel-Set-Sentry](https://docs.sentry.io/platforms/php/laravel/)

##### Test-Receive

使用postman在本地对添加的测试接口进行调用就可以在Sentry的项目面板中看到该次报错

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Sentry-Test-Receive.png" />


