---
title: Drupal8 Css and Javascript
date: 2017-11-16 17:55:13
categories:
  - Drupal
tags:
  - css
  - js
  - library
  - drupal8
description: Include css and js file in drupal8 theme.
---

## 定义一个library

### Library file name

{theme_name}.libraries.yml

### Library content define

``` yml
cuddly-slider:
  version: 1.x
  css:
    theme:
      css/cuddly-slider.css: {}
  js:
    js/cuddly-slider.js: {}
  dependencies:
    # 依赖其他jquery,drupal8 默认不再引入jquery库
    - core/jquery
```

** drupal8 在core/assets/vendor目录下有很多library库，这些库通过core/core.libraries.yml文件定义为drupal可用的library，依赖这些库只需要在dependencies下使用 core/{library_name}引用 **

** 在theme中定义的library，如上面的cuddly-slider，同样可以再其他theme中定义依赖，方式为{theme_name}/cuddly-slider **

## 文件加载顺序

默认情况下文件加载顺序与其library中的顺序一致，js文件默认加载在footer

可以通过header属性将js放到header加载，只需要定义在头部加载的文件本身，它所有的依赖会自动在头部加载

``` yml
js-header:
  header: true
  js:
    header.js: {}
```
