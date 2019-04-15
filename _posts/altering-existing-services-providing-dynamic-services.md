---
title: 'Altering existing services, providing dynamic services'
date: 2019-04-14 16:31:29
categories: Services and dependency injection in drupal 8
tags:
	- drupal8
	- services
description: Alter existing service using ServiceProviderBase and Custom compiler pass.
---

> [REF: Altering existing services, providing dynamic services](https://www.drupal.org/docs/8/api/services-and-dependency-injection/altering-existing-services-providing-dynamic-services)

### 修改存在的Service
- ServiceProvider
在模块根目录下创建ModuleNameServiceProvider class实现\Drupal\Core\DependencyInjection\ServiceModifierInterface或继承ServiceProviderBase
```
// 必须
namespace Drupal\my_module;

use Drupal\Core\DependencyInjection\ContainerBuilder;
use Drupal\Core\DependencyInjection\ServiceProviderBase;

// @note: You only need Reference, if you want to change service arguments.
use Symfony\Component\DependencyInjection\Reference;

/**
 * Modifies the language manager service.
 */
// 名字格式必须，继承类必须，方法必须
class MyModuleServiceProvider extends ServiceProviderBase {

  /**
   * {@inheritdoc}
   */
  public function alter(ContainerBuilder $container) {
    // Overrides language_manager class to test domain language negotiation.
    // Adds entity_type.manager service as an additional argument.
    $definition = $container->getDefinition('language_manager');
    $definition->setClass('Drupal\language_test\LanguageTestManager')
      ->addArgument(new Reference('entity_type.manager'));
  }
}
```

- 覆盖已存在的service
```
language_manager:
  class: Drupal\module_name\Service\MyLanguageManager
  arguments: ['@language.default']
```

*Finally, it is also possible to define the register() method to register services dynamically, however this should be very rare.*



