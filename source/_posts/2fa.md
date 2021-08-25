---
title: 双因素认证
date: 2021-08-24 14:30:11
tags:
---

##### 双因素认证

在认证用户时系统使用到的凭证即"因素"，常见的认证因素有账号密码，如果只有一种凭证，相当于单因素，双因素即有2种认证因素，本文双因素为密码+ 基于时间的一次性密码算法（TOTP）认证


##### TOTP

>基于时间的一次性密码算法（TOTP）是一种根据预共享的密钥与当前时间计算一次性密码的算法。它已被互联网工程任务组接纳为RFC 6238标准，成为主动开放认证（OATH）的基石，并被用于众多多重要素验证系统当中

<!-- more -->

TOTP有以下几个步骤

* 生成用户唯一的密钥
* 通过密钥与用户唯一标识（可以是手机号，用户名，邮箱等）生成二维码
* 使用专用扫码器扫码获取令牌保存
* 登陆时需要同时验证账号密码与保存的扫描器令牌码

##### RobThree/TwoFactorAuth

本文使用了第三方Php拓展包[RobThree/TwoFactorAuth git 仓库](https://github.com/RobThree/TwoFactorAuth)

##### 生成用户密钥
```php
$tfa = new RobThree\Auth\TwoFactorAuth();
$secret = $tfa->createSecret();
```

##### 生成二维码
```php
$tfa->getQRCodeImageAsDataUri('username', $secret);
// RobThree/TwoFactorAuth 使用的是CURL远程地址生成的二维码，国内访问有一定几率连接失败，可以使用另外的二维码生成器驱动
QrCode::size(200)->generate($tfa->getQRText('username',$secret));
// 调用getQRText方法获得文本再使用自行选择的QrCode生成方案生成二维码即可
// 本示例使用 simplesoftwareio/simple-qrcode

```
##### 使用专用扫码器扫码获取令牌保存
任意符合标准的扫码器均可 
* 腾讯身份认证器
<figure class="third">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-1.png" width="200" style="display: inline-block">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-2.png" width="200" style="display: inline-block">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-3.jpg" width="200" style="display: inline-block">
</figure>
  
* Google Authenticator
<figure class="third">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-4.jpeg" width="200" style="display: inline-block">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-5.jpeg" width="200" style="display: inline-block">
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/2fa-6.jpeg" width="200" style="display: inline-block">
</figure>

##### 验证令牌
```php
$tfa->verifyCode($secret, $code) === true
```
