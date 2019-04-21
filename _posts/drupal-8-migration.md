---
title: Drupal 8 Migration
date: 2019-04-21 14:05:48
tags:
	- drupal8
description: Drupal 8 migrate management.
---

### 简介
Drupal8核心提供了migrate模块实现基础migrate的功能。
Migrate按顺序分三个阶段，source-process-destination。source阶段获取的一组数据被成为row，这些数据可以来源于数据库，文件(csv,json,xml)或从其他网站提供的service(rss,rest,jsonapi)获取。source阶段获取的row被发送到process，process通过plugin对数据进行处理转换（也可以不进行操作），完成procees后，destination阶段将数据存储到相应的entity。
核心提供的migrate是由代码写入的，社区模块migrate_plus扩展了核心migrate功能，将迁移变得可配置，三个阶段具体要迁移的数据源，目的地址以及要使用的plugin的使用都在yml文件中进行配置指定

### 模块
- migrate: 核心模块，提供了迁移的基础功能以及一些插件
- migrate_tools: 社区模块，提供drush方法来操作迁移
- migrate_plus: 社区模块，提供了迁移扩展，扩展核心迁移功能，方便进行迁移
- migrate_drupal: 核心模块，将低版本drupal升级到drupal8

### 插件
Migrate的每一个阶段的执行都是在插件的协助下完成的，即能满足对多种类型数据处理的灵活性，同时也方便了我们定义自己的插件来进行特殊处理，以下简要介绍三个阶段的插件

##### Source
核心migrate中供使用的source插件很少，但是定义了SourcePluginBase，为其他source插件的定义提供基础功能，如track_changes,如设置为true，则所有被导入的记录都会被hash，为后续的数据更新提供对比
主要提供source插件的模块为migrate_plus，该模块提供了一个URL插件，该插件在实现的时候进一步插件化，将source过程划分成data_parser和data_fetcher，data_fetcher负责获取数据，模块提供了HTTP和FILE两个插件，分别通过http链接导入和本地文件导入；data_parser负责对获取来的不同类型的数据进行转换处理，提供了Json,SimpleXml,Soap,Xml等插件

##### Process
核心migrate模块中提供了很多可供使用的process插件(migrate/src/Plugin/migrate/process)，包括download, filecopy, log, migration_lookup, sub_process, skip_on_empty等。
Process插件可以对传入数据的每一个字段进行处理，以达到想要的值
写自己的插件可以参考系统提供的插件，同时社区模块也提供了很多这方面的插件

##### Destination
这里的插件主要是由核心提供的，具体可以查看核心插件(migrate/src/Plugin/migrate/destination)

### YML配置文件
由于YAM配置文件辅助迁移是有migrate_plus模块提供的，因此YML文件在命名上有固定的格式，首先YML文件位于config/install或config/option目录下，其次命名格式为migrate_plus.migration.{migrate_name}.yml
```
# migrate_plus.migration.product.yml 具体demo详见migrate_plus中的example
# 通过json文件导入数据.
# migrate唯一id
id: product
label: JSON feed of Products
migration_group: product
migration_tags:
  - json example
source:
  # 指定了source的插件
  plugin: url 
  data_fetcher_plugin: file
  data_parser_plugin: json
  # sites/default/files/migrate_json_example/products.json
  # 可以指定多个文件
  urls:
    - 'public://migrate_json_example/products.json'
  # json文件中的字段映射成我们需要的字段，供process取用
  item_selector: product
  fields:
    -
      name: upc
      label: 'Unique product identifier'
      selector: upc
    -
      name: name
      label: 'Product name'
      selector: name
    -
      name: description
      label: 'Product description'
      selector: description
    -
      name: price
      label: 'Product price'
      selector: price
  # 迁移数据的key值，是数据更新覆盖，以及重用的关键
  ids:
    upc:
      type: integer
# process阶段，调用plugin处理字段并将字段与destination字段进行mapping
# 这里相当于对destination entity的字段值进行赋值，只是赋值过程可以通过插件进行处理
process:
  type:
    plugin: default_value
    default_value: product
  title: name
  field_upc: upc
  field_description: description
  field_price: price
  sticky:
    plugin: default_value
    default_value: 0
  uid:
    plugin: default_value
    default_value: 0
# 保存到destination
destination:
  plugin: 'entity:node'
# 迁移依赖指定
migration_dependencies: {  }
# 模块依赖
dependencies:
  enforced:
    module:
      - migrate_json_example
```

### Table
在迁移执行时，每一条迁移数据会生成两条迁移数据，一条map数据，用来存储迁移前后的id映射，一条message来存储迁移时产生的问题
map表主要字段有source_ids_hash, sourceid1, destid1, last_imported, hash等，如果source中指定track_changes为true则hash字段会被写入。如果hash没有被写入，迁移会根据ids hash是否更新来判断是不是要迁移数据，如hash写入则根据hash来判断

### Execution
- drush ms：可以查看当前迁移状态，包括有那些可以迁移的配置生效了，源有多少数据，导入了多少，当前的导入状态
- drush mim {migrate_id}: 执行迁移，可以使用--update参数对migrate进行强制更新
- drush mrs {migrate_id}: 迁移异常中断，需要恢复为初始状态
- drush mr {migrate_id}: 回滚迁移，将已导入的数据清除，根据mapping表来进行的

*模块开启是所有的yml文件都会被存入config中，可以执行迁移操作，当yml文件修改时，config并不会自动修改，清缓存也不行，这时要对yml从新导入，可以使用'drush cim --partial --source=modules/custom/migrate_module/config/install'对该文件夹下的所有yml文件进行导入，也可以安装config_devel模块，使用'drush cdi1 \*yml'进行单个文件导入*

### Examples
具体例子请查看migrate_plus模块下的examples

### Tips
- 对于plugin使用请详细查看plugin上方的注释说明
- plugin的调用是管道执行的，前一个plugin的结果会作为下一个plugin的输入
- 对于富文本html的处理可以使用KubAT\PhpSimple\HtmlDomParser，将html转换成可操作的对象


