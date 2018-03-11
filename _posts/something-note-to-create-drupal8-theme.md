---
title: Something Note to Create Drupal8 Theme
date: 2017-11-11 22:16:13
categories: Drupal
tags:
	- drupal8
	- theme
	- debug
description: Create drupal8 theme.
---

## info文件
```
name: Next
type: theme
base theme: classy
description: 'A flexible, responsive theme copy hexo theme [next].'
package: Develop
core: '8.x'
version: '1.0.0'
# libraries in [themename].libraries.yml
libraries:
  - next/global-styling
  - next/global-script
# Theme中需要的regions，与page.html.twig文件中的page内部变量对应，这里定义了，page中才会出现相应的区域变量
regions:
  header: Header
  content: Content
  sidebar: Sidebar
  footer: Footer
```
## Base theme
Drupal8 theme子theme可以多层继承

## 核心theme介绍
- Stable：最简单的theme，基本没有为html标签添加class，相比classy来说更简单
- Classy：提供了drupal一些默认的class，例如body上的login-in，content-type等
- Bartik：继承了classy，作为Drupal默认的front theme
- Seven：继承了classy，作为Drupal默认的backend theme

## Debug
> Refer: [theme debug](https://www.drupal.org/docs/8/theming/twig/debugging-twig-templates)
### 开启theme debug
```
$ cd sites/default
$ cp default.services.yml services.yml
```
#### 修改services.yml中debug配置*
```
# twig.config:
#   debug: false
# 将debug配置为true
debug: true
```
*Principle: 在default目录下的settings.php中引入了theme配置文件services.yml，因此需要在services.yml中修改debug选项*

### Debug工具
#### xdebug
```
# 代码结尾没有;
# with module Devel
# 插入以下代码，并设置断点
{{ devel_breakpoint() }}
# with module Twig Xdebug
# try yourself.
{{ breakpoint() }}
```
没有xdebug，可以用这个。祝你好运，我的朋友
```
{{ dump(var) }}
```
## Theme缓存设置
为了提高效率，Drupal8的twig模版文件都会预先被编译成php文件，因此修改模版文件不会立即生效，需要清除缓存(drupal cr)来重新编译;可以通过配置使模板文件自动编译（当模板文件修改的时候）
```
# twig.config:
#   auto_reload: null
# 将auto_reload配置为true
auto_reload: true
```

## 模板Rewrite
> Refer: [Working With Twig Templates](https://www.drupal.org/docs/8/theming/twig/working-with-twig-templates)

### 模板Rewrite默认规则
> 模版重写默认的命名规则(https://www.drupal.org/node/2354645)

### 模版Rewrite hook
#### Hook List

- hook_theme_suggestions_HOOK(array $variables)
- hook_theme_suggestions_alter(array &$suggestions, array $variables, $hook)
- hook_theme_suggestions_HOOK_alter(array &$suggestions, array $variables)

##### Hook Order

>显示module，之后是base theme，最后是active theme；system weight不清楚是什么
>The call order is as follows: all existing suggestion alter functions are called for module A, then all for module B, etc., followed by all for any base theme(s), and finally for the active theme. The order is determined by system weight, then by extension (module or theme) name.

>针对性hook在通用hook之后执行
>Within each module or theme, suggestion alter hooks are called in the following order: first, hook_theme_suggestions_alter(); second, hook_theme_suggestions_HOOK_alter(). So, for each module or theme, the more general hooks are called first followed by the more specific.

##### Hook Demo

```
# Provide an alternative template suggestion to node and taxonomy term templates based on the user being logged in.

function MYMODULE_theme_suggestions_alter(array &$suggestions, array $variables, $hook) {
  if (\Drupal::currentUser()->isAuthenticated() && in_array($hook, array('node', 'taxonomy_term'))) {
    $suggestions[] = $hook . '__' . 'logged_in';
  }
}
```

### 模版原理

##### 不同于通用命名规则的另一种解析
模板的命名是根据页面的path来进行的，这里说的path是系统内部的URL(eg:node/1)。
首先将path分割成组件(eg: node/1/edit => node, 1, edit),首先设置模板前缀为page，然后每次拼接一个分割组件，每次拼接用双,最后将双替换为–，并在结尾加html.twig。
以上可以拼接出多种组件，覆盖规则为特殊模板覆盖通用模板。
特殊：如果页面为首页，那么模板为page–front.html.twig。