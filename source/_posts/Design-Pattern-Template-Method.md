---
title: Design-Pattern-Template-Method
date: 2020-05-19 14:00:03
tags: 
- Design-Pattern
---

##### 模版方法模式 

> 是一种让抽象模板的子类「完成」一系列算法的行为策略 
  是一种非常适合框架库的算法骨架。用户只需要实现子类的一种方法，其父类便可去搞定这项工作了。
  是一种分离具体类的简单办法，且可以减少复制粘贴，这也是它常见的原因。
  
<!-- more -->

##### Switch Case代码示例

业务场景：打印物品单，不同类型的物品单子格式不同

```php
switch ($type) {
     case fruits:
          print fruits application
          break;
     case tools:
          print fruits application
          break;
     case consumbles:
          print consumbles application
          break;
     ......
}

```

如上代码是很快能想到的一种方案，也是最简单易读的处理办法。不过我们会面临这样的问题：不同的单子格式，例如表头，表尾可能是一样的，而单子内容是肯定不同的，这个时候代码可以这样改
```php
switch ($type) {
     case fruits:
          getHeaderAndFooter
          getFruitsCotent
          break;
     case tools:
          getHeaderAndFooter
          getToolsCotent
          break;
     case consumbles:
          getHeaderAndFooter
          getConsumblesCotent
          break;
     ......
}

```
其中header和body都是统一的，而各自单子的内容又稍有不同，似乎满足了代码易读性和需求。但是新的需求又来了：每个单子的头尾可能也稍有不同。fruits是一种头尾风格，而tools和consumbles又是另外一种头尾风格（tools和consumbles相同），这时候最容易想到这样处理
```php

getHeaderAndBody($type){
    switch ($type) {
       case fruits:
            fruits header footer
            break;
       case tools:
       case consumbles:
            tools consumbles header footer
    }
}


```
这个时候就会发现，要想处理这么多类型不同风格的单子，似乎还得加上更多的类型判断，要是之后又出现更多细化需求，这里的类型可能又要加上一堆的if else来判断。久而久之代码层级就会越来越深，往往维护一个小单子的修改就可能要考虑到对其他单子的影响。

##### 模版方法模式代码示例

<img src="https://blog-image-1256046195.cos.ap-chengdu.myqcloud.com/Template-Method.png" />

```php
switch ($type) {
     case fruits:
          $fruits = new FruitsPage();
          $fruits->getContent();
          break;
     case tools:
          $tools = new ToolsPage();
          $tools->getContent();
          break;
     case consumbles:
          $consumbles = new ConsumblesPage();
          $consumbles->getContent();
          break;
     ......
}

```

如UML图所示，基类中设置了默认的getHeader getFooter方法和抽象方法getContent，每个不同的申请单必须实现自己的getContent设置不同的打印内容，而Header和Footer可以根据需求决定是否在各自类中重写实现。

##### 
