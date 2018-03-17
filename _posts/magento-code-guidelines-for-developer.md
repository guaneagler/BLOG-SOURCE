---
title: Magento code guidelines for developer
date: 2018-03-15 21:48:25
tags: 
	- magento
	- code standard
categories: magento
description: Translate magento technical guidelines
---
> Ref [Technical guidelines](http://devdocs.magento.com/guides/v2.2/coding-standards/technical-guidelines.html)

## 术语：
- MUST or MUST NOT (必须， 绝不能)
- REQUIRED (必要)
- SHALL or SHALL NOT (要， 不要)
- SHOULD or SHOULD NOT (应该， 不应该)
- RECOMMENDED (推荐)
- MAY （可能）
- OPTIONAL (可选的)

## 1. 基本编码原则
1.1. 函数变量不应该被修改
1.2. 明确的返回值必须在函数头部声明
1.3. 标量提示应该被使用
1.3.1. ** php使用严格模式？？**

## 2. 类设计
2.1. 对象必须遵循SOLID(S-单一职责，O-开闭原则[开放扩展，关闭修改]，L-里氏兑换[子类可以替换父类]，I-专有接口优于通用接口，D-依赖注入[依赖抽象])
2.2. 对象在实例化过后必须能直接使用方法，而没有多余的公有初始化方法
2.3. 类构造器只能有依赖注入赋值操作和参数验证操作，没有其他操作
2.3.1. 当构造器内部的参数验证失败时应该抛出异常
2.3.2. 事件绝不能在构造函数内触发
2.4. 所有的依赖必须使用客户端所要求的最通用的类型[抽象]
2.5. ** 代理和拦截器绝不能在构造器内部请求？？**
2.6. 不应该使用继承，应该使用组件[将一个类注入另一个]
2.7. 所有不需要公开的属性和方法都应该设置为私有
2.8. **抽象类绝不能被标记为公有的@api ？？**
2.9. *服务类(eg:提供方法而不是数据，像EventManager)不应该有一个可变的状态*
2.10. 只有数据对象(eg:Product, Category)可以有任意可变的状态
2.11. Setters不应该被使用，它们只用于数据传输对象
2.12. Getters不应该修改数据状态
2.13. 静态方法不应该被使用
2.14. 时间耦合必须避免
2.15. 方法链在类设计中必须被避免
2.16. ”最少知道“原则应该被遵守
2.17. 模式
2.17.1. 代理应该被用于延迟加载可选的依赖关系
2.17.2. Composites[复合，合成] SHOULD be used when there is a need to work with a tree as a single object
2.17.3. 策略应该在有多种执行操作的算法时使用

## 3. 依赖注入
3.1. 对象之间的依赖不应该形成闭环
3.2. app/etc/di.xml 只能包含框架级别的依赖注入
3.3. 所有module的依赖注入应该存储在module/etc/di.xml中
3.4. 所有module表现层的依赖注入应该存储在<module_dir>/etc/<area_code>/di.xml

## 4. 拦截（Plugin）
4.1. Around-plugin只有原有方法在某些场景下需要被替换时才能使用
4.2. Plugin不应该在本身module使用
4.3. Plugin不应该应用于数据对象
4.4. Plugin必须是无状态的

## 5. 异常
5.1. 所有呈现给终端用户的异常信息必须符合以下格式：
- 症状
- 详情
- 解决方案
5.2. 抛出异常和异常解决绝不能在同一个方法
5.3. A调用B，B抛出异常，A必须处理异常或者通过@throw来声明该异常
5.4. 异常绝不处理消息输出，应该有处理函数来决定如何处理异常输出
5.5. 业务逻辑绝不使用异常，应该用判断逻辑来替代
5.6. 异常类的名字必须清晰，有意义，标明异常产生的原因
5.7. 抛出的异常应该尽量特殊，最抽象的\Exception不应该被抛出
5.8. 所有与第三方类库的直接通信必须用try/cache包起来
5.9. \Exception只在调用第三方类库时被catch
5.10. \Exception不应该在前端控制器或者Action控制器抛出
5.11. A separate exceptions hierarchy SHOULD be defined on each application layer. It is allowed to throw exceptions that are only defined on the same layer
5.12. 如果异常处理与异常抛出的地方不属于同一层并且异常应该被抛出，那么应该新建一个异常实例适合当前的层。在这种情况下原始异常必须作为新的异常实例的“previous”参数传入
5.13. 不允许在没有log或任何异常处理的情况下消化异常
5.14. 任何异常都应该在他被处理的地方做log，异常不应该被重复抛出
5.15. 异常不应该在循环中处理，应该将整个循环放到异常处理中(try/catch)
5.16. 一个方法使用了系统资源(如files，sockets，stream)，代码都应该被try..finally包含，所有资源都应该在finally中释放
5.17. LocalizedException应该在呈现层(controller,block)抛出

## 6. 应用层
### 6.1. 所有层
6.1.1. 应用程序结构应该符合CQRS(Command Query Responsibility Segregation)原则(读写模块分离)
6.1.2. 每个应用层(呈现层，服务层，数据访问层)必须处理或抛出底层的异常
### 6.2. 呈现层
6.2.1. 根据CQRS原则，呈现层承载了命令和查询
- 命令 即 Action
- 查询 即 Layout和相应的元素(Block和UI Components)
6.2.2. Request, Response, Session, Store Manager and Cookie对象必须在呈现层使用
6.2.3. 所有Action必须返回ResultInterface的实例
6.2.4. Action决不能引用layout中声明的block
### 6.3. 数据访问(持久化)层
6.3.1. 实体字段可以被划分不同的范围(in product, EAV[entity attribute value] — per store, options — per website).
6.3.2. 每个持久化操作必须在一个范围集合内部执行
6.3.3. 实体不能包含持久化的逻辑操作
6.3.4. MYSQL的strict_mode的值应该与最新版本的MYSQL strict_mode默认值一致
### 6.4 服务层
待完成...

## 7. 配置
7.1. 一个应用程序应该包括
- 代码
- 环境配置文件
- 数据
7.2. 代码包括
- 应用程序代码库
- xml 配置文件
- 生成文件和静态文件
- 数据结构
- 系统配置值
- 配置范围(configuration scopes (stores/store groups/websites))
- CMS Entity
7.3. 环境配置包括应用程序服务的配置
7.4. 数据包括业务实体数据
7.5. 代码和环境配置绝不能放到数据中
7.6. 安装程序绝不能修改code
7.7. All XML configuration formats MUST be declarative. Imperative nodes are not allowed.
7.8. 所有的配置对象必须使用 Magento\Framework\Config

## 8. 模块化
8.1. 应用框架层不能依赖应用程序模块
8.2. 所有依赖必须在composer.json中声明
8.3. 如果A组件依赖B，那么在composer.json中，A的声明必须包含require B
8.4. 如果A组件要继承或修改B通过B的一些定制点(layout,plugin,event)，那么A的suggest部分要声明B
8.5. Only the @api code of any module can be referenced by other modules.
8.6. 模块绝不能包含对theme文件的引用
8.7. A component MUST NOT rely neither on dependencies of dependencies nor on dependencies of the project it is included in (e.g., Magento application). All component dependencies MUST be stated explicitly.

## 9. 在web程序中浏览器服务器交互
9.1. 所有客户端服务器必须遵守http协议
9.2. 所有客户不可知的数据(Product，Catelogries，CMS Page)必须由服务器渲染并缓存在vanish中
9.3. 不理解。。后续。。

## 14. Events
Event的Observer不应该修改event传入的数据，相对的Plugin应该对传入的数据做修改
