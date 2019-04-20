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

- 最后可以通过定义register()方法来动态的注册service，但是很少见

### Custom compiler pass
Compiler pass是Container的扩展点，你可以用它覆写已存在service中的方法或添加新的方法。你也可以自己创建一个新的tag点供其他模块使用（如event_subscriber）
> During the compilation, declaration files are parsed, parameters and configuration are resolved, "alias" are created and tagged services are processed.

在清缓存的过程中，Container会被编译，在编译过程中，所有container管理的service都会被注册到container中。在编译完成后，container会被缓存。
大多数情况下我们都不需要创建compiler pass，但是过程需要了解一下

##### Drupal如何编译container
- index.php
	- ...
	- $response = $kernel->handle($request);
		- ...
		- $this->boot();
			- ...
			- $this->initializeContainer(); 初始化容器
				- ...
				- $container = $this->compileContainer();
					- $this->initializeServiceProviders();
						- $this->discoverServiceProviders(); 将\*.service.yml文件和\*ServiceProvider class分别存储到serviceYamls和serviceProviderClasses中
						- ... 将实例化的\*ServiceProvider类放入serviceProvider中
					- ... 注册services到container
					- $container->compile();
	- ...

##### 创建compiler pass
```
\compiler_module
	src\CompilerModuleServiceProvider
	src\Compiler\CompilerModuleExtensionPass

// CompilerModuleServiceProvider
namespace Drupal\compiler_module;

use Drupal\Core\DependencyInjection\ContainerBuilder;
use Drupal\Core\DependencyInjection\ServiceProviderBase;
use Drupal\compiler_module\Compiler\CompilerModuleExtensionPass;

class CompilerModuleServiceProvider extends ServiceProviderBase {

  /**
   * {@inheritdoc}
   */
   public function register(ContainerBuilder $container) {
      $container->addCompilerPass(new MyCustomExtensionPass());
   }
}


// CompilerModuleExtensionPass
namespace Drupal\compiler_module\Compiler;

use Symfony\Component\DependencyInjection\Compiler\CompilerPassInterface;
use Symfony\Component\DependencyInjection\ContainerBuilder;

class CompilerModuleExtensionPass implements CompilerPassInterface {

    /**
    * {@inheritdoc}
    */
    public function process(ContainerBuilder $container) {
        ... your work
    }

}
```
