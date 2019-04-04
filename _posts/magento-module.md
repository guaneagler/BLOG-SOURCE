---
title: magento module
date: 2018-04-14 12:57:54
tags:
	- module
categroies: magento2
description: Magento module learning. 
---

## Module必要组成
registration.php 注册
module.xml
	-	name
	- version
	- dependency

## 模块依赖
一个模块只实现一个作用，模块依赖其他模块必须要声明，删除一个模块不能影响到其他模块的工作
依赖有soft dependency 和 hard dependency，soft是即使依赖被删除，仍能正常工作，因此是允许的；相对的hard dependency则是没有依赖模块，当前模块无法正常工作，这样的依赖是必须要声明的

