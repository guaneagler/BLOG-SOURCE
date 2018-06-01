---
title: Drupal Webform
date: 2018-06-01 16:37:14
categories: Drupal
tags:
	- drupal
	- webform
	- entityform
description:
	Introduce drupal webform and entityform.
---

## Webform

#### Summary
Webform安装完成后，首先会生成一个webform的content type，而用户所要新建的webform则属于content类型，每一个webform都是一个node

#### Main features
- 提供page-break，可以让webform实现多页面问答.eg:下一题
- 提供group，可以对form表单进行分组
- 提供markup，可以放一些静态文字
- 提供placehoulder，字段长度，必填等功能
- 提供Date, email, file, hidden, number select等form类型的表单
- 提供邮件功能，可以发送邮件给form里的邮箱用户
- 可以自定义form提交后的跳转页面
- 可以添加表单验证规则
- webform的结果提供了表格格式，分析格式,并且附带了下载功能

#### Configuration

- 创建一个webfrom类型的content
- 编辑新创建的node，在webform tab下面对webform进行编辑(form component, conditionals, email, form settings)
1. 在form components下，可以添加各种类型的form字段, 除了添加form字段，page break，fieldset也可以在这里添加
2. 在conditionals下，可以添加各种表单验证条件
3. 在Email下，可以编辑form提交后发送邮件内容以及收件人等信息
4. 在Form settings下，可以编辑当前webform的配置信息,如跳转,多次提交限制等
- results展示了所有提交的结果，并提供下载功能
- webform在后台的configuration也提供了配置，如邮件配置，匿名用户区分等


## Entityform

#### Summary
Entityform在安装完成之后，会创建一个名为Entityform Type的Entity type，类似structure下面的content type.而用户创建的是一个类似一个具体的content type.

#### Main features
- 创建的entityform类似一个新的content type，它是通过添加新的field来创建表单的，field的类型就是content type里的filed类型

## Conpare
对于创建一个form来说，webform应该是首选了，提供的功能比较全面，但是它保存的result由于并不属于node类型，可能后期处理起来就有些麻烦,同时由于webform是一个node，因此默认的feature导出是不支持的，有一个webform_feature可以提供这个功能，由于webform是node，因此做页面可能就有一些限制，不那么灵活，我的解决方式是将这个node直接塞到page manager或者先塞到view block，再塞到page manager.
对于entityform，我现在感觉它除了提供一个比webform更为规则的结果集合以外，暂时没发现有哪些优点，因此不知道这个的适用场景
