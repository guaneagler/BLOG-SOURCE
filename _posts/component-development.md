---
title: 组件开发
date: 2018-03-17 12:37:29
tags:
- magento
- sevice contracts
- interface
- API
- Dependency injection
- component
categories: magento
description: Magento 模块开发
---

> Ref [Component development](http://devdocs.magento.com/guides/v2.2/extension-dev-guide/module-development.html)

## Service contracts
#### 源由：
Magento允许第三方开发者修改系统固有逻辑，很有灵活性，但也有代价。
开发者可以在各个层插入逻辑，这就导致代码重复和不一致性
这就导致使用了第三方组件的用户不敢轻易的升级系统由于第三方组件的不确定性，第三方组件开发者很难跟上系统升级速度由于不确定自己的组件和其他组件是否有冲突。
#### 服务约束
服务约束包括 数据接口-保持数据完整性；服务接口-隐藏实现细节。
遵从服务约束的开发者，组件有定义好的输出和稳定的api。其他开发者就可以遵从这个约束来进行开发
#### 优点
遵从服务约束可以方便其他开发者具体实现
数据模型定义抽象可以方便更换其他数据库，然后再进一步实现具体方法

## Public interface & API
#### Public interface
公有接口是第三方开发者可以调用，实现，用作插件的代码集合。Magento保证公有接口在大版本不变的情况下保持不变
公有接口要以 @api 声明
第三方开发者应该调用有@api声明的接口，当然其他的接口也可以调用，但是不保证稳定性
#### API
API是一个模块提供给其他模块提供的接口和实现

## Service contract design patterns
设计模式是一种编码推荐方式，告诉你什么时候用什么时候不用，即在某种情况下的最佳实现
服务约束设计模式告诉你定义那一种接口，怎么定义在哪里定义接口。
#### 类型和位置
两种类型：数据接口-保证数据完整性；服务接口-隐藏细节提供规范
数据接口返回信息包括数据实体，返回查询结果，设置查找条件，返回相应条件下的查询结果。数据接口定义在Api\Data目录下
服务接口包括操作管理<management>，仓库<repository>和元数据<metadata>接口。服务接口定义在Api目录下
服务接口
- 仓储接口<Repository Interface> 提供持久化数据的途径，如save(), get() delete(), deleteById(), getList(search criteria)
- 操作接口<Management Interface> 提供非仓储接口，类似业务逻辑类型的,如 createAccount(), changePassword(), validate()等

## Dependency injection 依赖注入
Magento2用依赖注入替代了1里面的Mage类
依赖注入是一种设计模式，允许A声明对B的依赖，并使用B提供的方法，A在声明B的依赖的时候一般都声明B的接口，这样就达到了松耦合的目的，A不需要知道B内部如何实现，只需要知道B可以做什么，并调用就可以了
#### 原则
尽量使用接口声明依赖，解耦合，并且降低了升级后不兼容的问题的概率
#### Object Manager
ObjectManager 是一个将相应依赖插入相应对象的类，即依赖注入的实现着，一般情况下不需要手动调用它。特殊情况是复杂的custome factory或者集成测试
#### 编译依赖 <Compiling dependencies>
Magento 将所有的依赖存储在文件中，在对象创建过程中，ObjectManager使用这些信息创建依赖
Service classes that do not exist in the codebase, such as proxies, factories, and interceptors that are declared in code or in a configuration, are generated with the help of the compiler.
#### 两种注入方式： 构造器注入和方法注入
#### 注意： 注入对象不能形成依赖闭环；有些对象不能被注入，只能通过new来实例化，瞬态对象如那些通过用户或者外部输入来生成的对象，不能被注入，如product对象，我们至少需要product的id才能得到一个product对象，但是这种对象可以通过先注入factory对象，再有factory对象创建该对象

## ObjectManager
#### 职责
> 在工厂和代理中创建代码
> 在单例模式中创建共享对象当用户请求时
> 依赖管理，当构造器请求接口时，实例化相应的对象
> 自动实例化构造器中的参数
#### 配置
di.xml文件定义告诉ObjectManager如何实例化对象
告诉objectmanager为定义的接口实例化哪个类
是否为每一个请求创建一个对象还是单例模式
#### 使用规则


