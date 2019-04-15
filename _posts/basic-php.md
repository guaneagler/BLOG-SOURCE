---
title: Basic PHP
date: 2019-04-14 18:32:22
tags:
	- php
description: Record some PHP knowledge.
---

### use
use \a\b\c 是 use \a\b\c as c的缩写，所以use关键字只是相当于创建一个别名，没有其他用途

### SPL
是 Standard PHP Library 的缩写

### spl_autoload_register
> [spl_autoload, __autoload and spl_autoload_register](https://stackoverflow.com/questions/7651509/what-is-autoloading-how-do-you-use-spl-autoload-autoload-and-spl-autoload-re)
[Autoloading Classes](https://www.php.net/manual/en/language.oop5.autoload.php)

在面向对象写法中，我们需要实例化PHP类，就要include类文件，如果类很多，就要引入很多类文件，为了解决这个问题，从PHP5开始，使用Autoloading Classes解决这个问题.
```
// 该方法显示声明了在类实例化的时候要调用的方法
spl_autoload_register('myAutoloader');
// 实例化类的时候调用的方法，该方法找到并引入相应的类文件
function myAutoloader($className) {
    $path = '/path/to/class/';
    include $path.$className.'.php';
}
// 类实例化，实例化的时候会去调用myAutoloader方法
$myClass = new MyClass();
```
