---
title: Drupal & Design Pattern
date: 2019-03-30 15:37:04
tags:
  - Drupal doc
description: 'None'
---

> Ref: [A Journey Towards Drupal 8 Development Mastery (3): Design patterns](http://icelark.com/our-thinking/journey-towards-drupal-8-development-mastery-3-design-patterns)

## Design Pattern

#### 什么是设计模式
设计模式通常是软件开发中常见问题的公认解决方案。它意味着一种完善的结构化代码方法，可以解决软件开发中特定的常见挑战。这些模式被广泛使用，经过充分测试并被证实是解决问题的最佳解决方案。

#### 为什么设计模式如此重要？
1. 提供了公认的解决问题的方案，省时省力，不需要你花更多的时间去做。
2. 让熟悉这种设计模式的人更容易的了解你的代码
3. 对于一个大型系统，例如Drupal，设计模式的存在让大家对于问题的解决方案趋于一致，不会乱。如果每个人都用自己的方式解决问题，那么最后形成的Drupal必然是混乱难以理解的。

> [Five common PHP design patterns](http://www.ibm.com/developerworks/library/os-php-designptrns/)
> [The Whens and Whys for PHP Design Patterns](http://code.tutsplus.com/tutorials/the-whens-and-whys-for-php-design-patterns--net-27862)

Drupal 8 中广泛使用的的三种设计模式
1. 面向对象
2. 工厂模式 - 一个返回另一个Object的Object
3. 依赖注入 - 最小化依赖和创建易于维护的代码

> 命名空间 & PSR-4
> 命名空间的引入为开源软件提供了极大的便利，它使不同开发者贡献的不同模块不会因为有相同的类名而产生冲突
> PSR-4：遵循PSR-4的规则，就可以通过命名空间引入Class
