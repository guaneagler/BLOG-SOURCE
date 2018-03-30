---
title: Magento2 code sniffers
date: 2018-03-29 15:36:01
tags:
	- magento
	- code standard
	- phpstorm
categories: magento
description: Config magento code sniffers in phpstorm.
---

> [Magento code sniffer](http://devdocs.magento.com/guides/v2.2/coding-standards/code-standard-sniffers.html)
> [Code standard provided by magento](https://github.com/magento/marketplace-eqp)
> [Phpstorm config](https://www.atwix.com/magento-2/configure-code-sniffer-for-phpstorm/)

## 帐号创建
1.首先在https://marketplace.magento.com/上注册帐号
2.进入个人中心，创建Access Keys
3.Access Keys创建后会包含一个公钥一个私钥

## 下载code sniffer
1.找一个独立的位置，执行以下代码，下载code sniffer的库
```composer create-project --repository=https://repo.magento.com magento/marketplace-eqp magento-coding-standard```
2.弹出提示输入用户名密码，用户名为上面创建的‘Access Keys’中的公钥，密码为私钥

## 配置phpstorm
1.进入phpstorm配置 Preferences->Languages&Frameworks->PHP->Code Sniffer and push the “…” button
2.弹出框中选择phpcs的路径，phpcs在我们上一步下载的代码中,选择这个路径下（magento-coding-standard/vendor/bin）的phpcs
3.点击validate，应该提示成功

4. 进入phpstorm配置 Preferences->Editor->Inspections and choose PHP-> PHP Code Sniffer validatation
5. 配置codestandard，首先点击刷新，然后点击下拉按钮，选择MEQP2.z
