---
title: Service Tags
date: 2019-04-20 16:48:08
tags:
	- drupal8
categories: Services and dependency injection in drupal 8
description: Drupal 8 services tags, event and subscriber
---
Drupal 8 api定义了service_collector，做成了类似event subscriber的机制。service collector是事件，依赖这个事件的service是subscriber。当这个collector被触发的时候，subscriber也会进行相应

#### Collector 定义
```
# core.services.yml:
string_translation:
  class: Drupal\Core\StringTranslation\TranslationManager
  arguments: ['@language.default']
  tags:
    - { name: service_collector, tag: string_translator, call: addTranslator }
```
这个service_collector的name是collector区别于普通的service的地方
tag是订阅的service在tag里的关键字
call是订阅者被找到的时候调用的方法
```
// \Drupal\Core\StringTranslation\TranslationManager:
public function addTranslator(TranslatorInterface $translator, $priority = 0) {
  $this->translators[$priority][] = $translator;
  // Reset sorted translators property to trigger rebuild.
  $this->sortedTranslators = NULL;
  return $this;
}
```
如上subscriber是第一个参数，第二个是优先级

下面是一个调用tag的service
```
# core.services.yml:
string_translator.custom_strings:
  class: Drupal\Core\StringTranslation\Translator\CustomStrings
  arguments: ['@settings']
  tags:
    - { name: string_translator, priority: 30 }
```

