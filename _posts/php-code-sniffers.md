---
title: Php code sniffers
date: 2018-06-04 15:11:37
categories: PHP
tags:
	- drupal
	- magento
	- code-sniffers
	- phpcs
	- phpstorm
description: Instruction of phpcs and config phpcs for drupal and magento in phpstorm
---

### Summary
PHP配置code review由两部分组成，一个是phpcs，它是一个php脚本，用来检测并执行相应的code-standard;另一个是code-standard,提供code review规则.

### phpcs 常用命令
```
# 列出所有phpcs能检测到的codestandard
phpcs -i

# 配置phpcs检测code-standard的目录,目录下的code-standard文件夹必须有自己的ruleset.xml文件
phpcs --config-set installed_paths <dir with ruleset.xml>

# 显示所有phpcs配置
phpcs --config-show

# 删除某个配置
phpcs --config-delete <key>
```

### Phpstorm配置
Phpstorm需要配置的地方有两个，一个是phpcs可执行文件的路径，在settings> Languages&Frameworks> PHP> Code Sniffer; 另一个是配置phpcs检测到的code-standard,在settings> Editor> Inspections> PHP> PHP Code Sniffer validation. 配置standard的时候需要点击一下刷新按钮，点击下拉就可以看到所有被检测到的code-standard

### Magento and Drupal code-standard

#### Drupal code standard
下载drupal的coder module即可，里面的code_sniffer有Drupal和DrupalPractice两个standard
#### Magento code standard
请看[Mangento2 config code sniffer](/2018/03/29/Magento2-code-sniffers/)
