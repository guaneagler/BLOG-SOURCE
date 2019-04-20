---
title: Drupal 8 services and dependency injection
date: 2019-04-14 15:08:45
categories: Services and dependency injection in drupal 8
tags:
	- drupal8
	- services
description: Drupal 8引入services的概念，使重用的功能解耦，使这些services插件化，并且可以通过注册到容器来替换services
---

> [REF: Services and dependency injection in Drupal 8](https://www.drupal.org/docs/8/api/services-and-dependency-injection/services-and-dependency-injection-in-drupal-8)

Service是任何被Service Container管理的对象，-- Drupal8. Container是管理service的容器。Drupal8提出了service的概念来解耦和重用功能，并通过将service注册到容器中使service可插拔可替换。作为开发者，最佳做法是通过container来调用任何service以保证系统松耦合。[Symfony文档](https://symfony.com/doc/3.4/service_container.html)详细介绍了service

作为开发者，service提供了诸如访问数据库，发邮件等功能。比起调用php默认的sql方法，我们使用container调用系统service来进行操作，这样我们可以直接调用service访问数据库而不需要担心是MySQL还是SQLlite, 或者发邮件是通过SMTP还是其他方法

### 核心Services
核心services定义在[core/core.services.yml](https://api.drupal.org/api/drupal/core%21core.services.yml/8)和[core/lib/Drupal/Core/CoreServiceProvider.php](https://api.drupal.org/api/drupal/core%21lib%21Drupal%21Core%21CoreServiceProvider.php/8.2.x)中

当我们需要查找一些services的时候，可以在core.services.yml中查看主要的核心services

### 使用Services
使用Services的最佳方式不是使用全局的services容器，而是通过contructor注入或者通过services setter方法

- 依赖注入
将一个对象依赖的services明确的传入对象被称为依赖注入
```
# core.services.yml
path.alias_manager:
  class: Drupal\Core\Path\AliasManager
  arguments: ['@path.alias_storage', '@path.alias_whitelist', '@language_manager', '@cache.data']	

# Drupal\Core\Path\AliasManager
public function __construct(AliasStorageInterface $storage, AliasWhitelistInterface $whitelist, LanguageManagerInterface $language_manager, CacheBackendInterface $cache) {
  $this->storage = $storage;
  $this->languageManager = $language_manager;
  $this->whitelist = $whitelist;
  $this->cache = $cache;
}	
```

- 使用全局方法
Drupal提供了一些封装的静态全局方法可以直接访问某些services，如Drupal::translation()，如果不存在封装好的方法，可以使用Drupal::service(@service_id).

*尽量用依赖注入的方式调用service*

### 定义自己的Service
在module.service.yml中定义自己的service
```
services:
  test.test_service:
    class: Drupal\test\Service\TestService
    tags:
      - { name: path_processor_inbound, priority: 101 }
    arguments: ['@path.alias_manager']
```
