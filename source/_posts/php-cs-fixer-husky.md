---
title: Php-Cs-Fixer+Husky
date: 2020-04-02 11:50:25
tags:
   - Php
   - Git
   - Laravel
---


##### 前言

> **PHP CS Fixer**  
The PHP Coding Standards Fixer (PHP CS Fixer) tool fixes your code to follow standards; whether you want to follow PHP coding standards as defined in the PSR-1, PSR-2, etc., or other community driven ones like the Symfony one. You can also define your (team's) style through configuration.
> **Husky**
Git hooks made easy

`php-cs-fixer` 是一个代码格式修复工具，它可以使你的代码复合各项标准规范或者自定义规则
`husky` 是一个`git hooks` 工具  [Git Hooks 详见](https://git-scm.com/book/zh/v2/%E8%87%AA%E5%AE%9A%E4%B9%89-Git-Git-%E9%92%A9%E5%AD%90)


<!-- more -->


##### Laravel 项目安装 `php-cs-fixer` `husky`

[php-cs-fixer git 仓库](https://github.com/FriendsOfPHP/PHP-CS-Fixer)
```
composer require friendsofphp/php-cs-fixer
```
[husky git 仓库](https://github.com/typicode/husky)

```
#注意husky需要 Node >= 10 and Git >= 2.13.0
npm install husky --save-dev
```

在项目的package.json 添加
```
"husky": {
        "hooks": {
            "pre-commit": "vendor/bin/php-cs-fixer fix --using-cache=no"
        }
    }
```

在项目目录下
```
touch .php_cs
```
[php_cs内容参考](https://gist.githubusercontent.com/ShahinSorkh/f8d9284950f2f073aaa2c2ff11300321/raw/.php_cs)


之后commit前就会触发php_cs自定义的内容对代码格式进行fix

也可以直接在项目目录运行vendor/bin/php-cs-fixer fix --using-cache=no查看结果
```
Loaded config default from "/your_code_file_path/.php_cs".
   1) xxxx.php
   2) xxxx.php
   3) xxxx.php
   # fix的文件
```

##### PhpStorm配置

> 项目设置了php-cs-fixer后，在phpstorm中会对没有按照规则设置的代码作出警示，可以通过设置一键补全格式

* 设置external tools
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/add-external-tools.png" />
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/external-tool-setting.png" />
```
#Program 是安装的php-cs-fixer包的位置
#Arguments  fix --verbose --config=$ProjectFileDir$/.php_cs --path-mode=intersection "$ProjectFileDir$"  #该项目路径下放.php_cs配置
#Working diretory 设置常量 $ProjectFileDir$  
```

* 设置快捷键
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/keymap-setting.png" />

* [文章参考](https://medium.com/hackernoon/how-to-configure-phpstorm-to-use-php-cs-fixer-1844991e521f)


##### 常见问题

```
# MacOS 10.15 Catalina

dyld: Library not loaded: /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib
  Referenced from: /usr/local/bin/php
  Reason: image not found
  
#解决办法
brew uninstall vapor
brew install vapor/tap/vapor

参考：https://stackoverflow.com/questions/59006602/dyld-library-not-loaded-usr-local-opt-openssl-lib-libssl-1-0-0-dylib
```

```
phpstorm commit没有触发git hooks
在phpstorm的commit 界面查看是否勾选 run git hooks
```
<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/phpstorm-git-hooks.png" width = 256 />

