---
title: Magento 01
date: 2018-04-14 10:25:43
tags:
	- magento-install
	- composer
	- magento-architecture
categories: magento
description: Magento概述
---

## 模式设置
Magento有三种模式，Default， Developer, Production. 默认是Default。
开发中为了显示错误信息，我们设置模式为Developer，在magento根目录执行命令
```
bin/magento deploy:mode:set developer
```

## 区域
Magento有6个区域: adminhtml, frontend, crontab, REST Web API, SOAP Web API, Install.
我们主要接触的就是adminhtml和frontend，不同区域的配置文件只在自己的区域内加载

## Magento2的一些重要概念
1.Module. Magento2是一个模块化的系统<有一些特例，如系统框架文件和一些前端生成的静态文件>
2.Theme定义了网站如何显示，它包含了各种静态资源文件，当然，有一些静态文件存在于module中，theme和module是相通的，总体上来说，module包含php逻辑文件，即后端；而theme包含前端文件。
3.Layout是magento2显示必不可少的部分，它由很多xml文件组成，这些文件定义了在页面上显示哪些元素。layout文件定义了显示哪些block以及block的顺序和层级。Block文件和它对应的templage文件则定义了显示内容。Layout文件是可配置可扩展的，你可以通过在自己的module创建layout update文件来修改已经存在的layout
4.配置文件.Magento有很多配置文件，如event.xml，di.xml，route.xml等，这些文件分布在各个module中。在向magento发出请求的时候，magento会组合同类别的所有文件，得到配置信息
5.DI，你可以通过在constructor中声明类，magento2会自动注入类对象，建议声明接口或抽象类，增加可扩展性。
6.命名空间，magento的路由和controller文件的定义，以及目录结构有关系，当然还有route.xml文件。layout文件的命名又是根据路由来的
7.Events和Plugins是两个修改magento实现的方式。用户可以实现observer来监听event，在observer中实现自己要做的事情；用户可以创建plugin来改变原有的方法之前和之后的实现

## Magento2结构
Component | Location | Comment
----------|----------|--------
Configuration | app/etc/ | env.php, config.php
Framework | library/internal/Magento/Framework | Framework class
Modules | app/code/Magento | business logic
Command-line tool | bin/magento | clear cache, generate static file
Themes | app/design | Contain static file belong to a theme
Dev tools | dev | testing framework and sample data.

| Composer installation | Clone Repo
---|---|---
Modules | vendor/magento/module-* | app/code/Magento/*
Framework | lib/internal/Magento/Framework/* | vendor/magento/framework
Themes | vendor/magento/theme-frontend-* | app/design/frontend or adminhtml/Magento/*

## 执行你自己的custome code
- 创建module
- 执行 bin/magento setup:upgrade 来执行Setup/Upgrade的脚本(脚本所在的module要升级过，才会执行)
- 通过plugin来修改core的public方法
- 通过创建observer监听core event来做自己的事情
- 将你的class注入到核心类中
- Controller
- 系统配置

## 目录功能
- app: 存放module和theme
- bin: 命令行
- dev: 一些测试和工具
- lib: 一些未通过composer安装的lib，可能含有核心框架
- vendor: composer安装的依赖，可能包含系统核心和module
- pub: 系统上线的时候所有的静态文件都会放到这里
- setup: 安装和更新文件
- var: 缓存和log文件
- generate:生成的php类