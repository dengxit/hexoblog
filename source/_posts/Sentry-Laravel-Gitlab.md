---
title: Sentry-Laravel-Gitlab
date: 2020-03-27 13:10:15
tags:
 - Sentry
 - Laravel
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

##### Set-Exception-Level

> You can set the severity of an event to one of five values: fatal, error, warning, info, and debug. error is the default, fatal is the most severe and debug is the least severe.

Sentry的默认消息通知发送是error级别的，如果代码中有设置一些诸如逻辑验证的异常抛出应当设立正确的错误级别
文档中提供了一种实现方式，这样在handler的异常报告处理时可以根据捕捉到的异常类型设置level
```
Sentry\configureScope(function (Sentry\State\Scope $scope): void {
  $scope->setLevel(Sentry\Severity::warning());
});

```
在 项目>警报 中也可以设置发送警报的规则

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Sentry-Alert-Level.png" />



