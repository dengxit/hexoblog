---
title: bash
date: 2019-12-26 17:34:00
tags:
 - bash
---
##### jq
```
https://jqplay.org/

https://stedolan.github.io/jq/manual/#Basicfilters
```


##### 管道的操作符 |

前一条指令的输出流向会作为后一条指令的标准输入
```
ls | grep  "Android"
```


##### Set
```
set -ex

-x 运行结果之前，先输出执行的那一行命令
-e 脚本只要发生错误，就终止执行

http://www.ruanyifeng.com/blog/2017/11/bash-set.html
```


##### Sed

```
sed 's/要被取代的字串/新的字串/g'

echo 'test/1.x'' | sed 's/tag\///' |tr '.' ' '

#输出
1 x
```

##### 文件测试运算符
```
-b file	检测文件是否是块设备文件，如果是，则返回 true
-c file	检测文件是否是字符设备文件，如果是，则返回 true
-d file	检测文件是否是目录，如果是，则返回 true
-f file	检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true
-g file	检测文件是否设置了 SGID 位，如果是，则返回 true
-k file	检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true
-p fill	检测文件是否是命名管道，如果是，则返回 true
-u file	检测文件是否设置了 SUID 位，如果是，则返回 true
-r file	检测文件是否可读，如果是，则返回 true
-w file	检测文件是否可写，如果是，则返回 true
-x file	检测文件是否可执行，如果是，则返回 true
-s file	检测文件是否为空（文件大小是否大于0），不为空返回 true
-e file	检测文件（包括目录）是否存在，如果是，则返回 true
```

##### 输出重定向
```
2>&1  意思是把 标准错误输出 重定向到 标准输出.

/dev/null是一个文件，这个文件比较特殊，所有传给它的东西它都丢弃掉
```

##### screen

> 只要Screen本身没有终止，在其内部运行的会话都可以恢复。这一点对于远程登录的用户特别有用——即使网络连接中断，用户也不会失去对已经打开的命令行会话的控制。只要再次登录到主机上执行screen -r就可以恢复会话的运行。同样在暂时离开的时候，也可以执行分离命令detach，在保证里面的程序正常运行的情况下让Screen挂起（切换到后台）
```
screen -S example  #开启一个名为 example 的 screen
control A 再按 D # 离开当前的screen，放入后台执行
screen -ls  #展示当前挂载的所有screen
screen -r example #回到 example screen
control D #将该作业结束
screen -wipe #检查并删除无法使用的screen
```
