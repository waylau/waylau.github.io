---
layout: post
title: 《重构》笔记
date: 2016-05-22 01:41
author: admin
comments: true
categories: [Refactoring]
tags: [Refactoring,重构,笔记]
---

对 Martin Fowler 的《重构：改善既有代码的设计》一书所做的笔记和总结。

<!-- more -->

# 书籍信息：
## 出版信息

* 作者: Martin Fowler 
* 出版社: 人民邮电出版社
* 副标题: 改善既有代码的设计
* 原作名: Refactoring: Improving the Design of Existing Code
* 译者: 熊节 
* 出版年: 2010
* 页数: 428
* 定价: 69.00元
* 装帧: 平装
* 丛书: 图灵程序设计丛书
* ISBN: 9787115221704

## 内容简介

重构，一言以蔽之，就是在不改变外部行为的前提下，有条不紊地改善代码。多年前，正是本书原版的出版，使重构终于从编程高手们的小圈子走出，成为众多普通程序员日常开发工作中不可或缺的一部分。本书也因此成为与《设计模式》齐名的经典著作，被译为中、德、俄、日等众多语言，在世界范围内畅销不衰。

本书凝聚了软件开发社区专家多年摸索而获得的宝贵经验，拥有不因时光流逝而磨灭的价值。今天，无论是重构本身，业界对重构的理解，还是开发工具对重构的支持力度，都与本书最初出版时不可同日而语，但书中所蕴涵的意味和精华，依然值得反复咀嚼，而且往往能够常读常新。

## 作者简介

Martin Fowler 世界软件开发大师，在面向对象分析设计、UML、模式、XP和重构等领域都有卓越贡献，现为著名软件开发咨询公司ThoughtWorks的首席科学家。他的多部著作《分析模式》、《UML精粹》和《企业应用架构模式》等都已经成为脍炙人口的经典。

其他参编者——
Kent Beck 软件开发方法学的泰斗，极限编程的创始人。他是 Three Rivers Institute 公司总裁，也是 Agitar Software的成员。
John Brant 和 Don Roberts The Refactory 公司的创始人，Refactoring Browser （http://st-www.cs.illinois.edu/users/brant/Refactory/）的开发者，多年来一直从事研究重构的实践与理论。
William Opdyke 目前在朗讯贝尔实验室工作，他写的关于面向对象框架的博士论文是重构方面的第一篇著名文章。

# 重构

## 重构的含义

重构，就是在不改变代码外部行为的前提下，对代码进行修改，以改进改善程序的内部结构。

* 程序整理方法
* 最大程序减少整理过程中引入错误的几率
* 在代码写好之后改进它的设计

## 重构的原因

* 规模大、历史久、代码质量差、添加单元测试或理解其内部逻辑成为不可能的任务
* 难以理解软件的行为
* 软件不一定一开始就是正确的
* 随着时间推移，软件的实现脱离了最初的设计
* 任何傻瓜都能写出计算机能理解的代码。唯有写出人类容易理解的代码，才是优秀的程序员。
* 天才程序员只是少数，大多数人不可避免会犯错
 

## 重构目的

* 消除重复
* 使代码易理解、易修改
* 改进软件的设计
* 查找 bug，提高质量
* 提高编码效率

## 重构的技巧

* 准从规则。编码、分层、归类
* 小步执行，保证每步都正确。小修改、测试、小修改、测试......
* 系统化进行
* 频繁测试。确定一套测试机制
* 用绝对安全的行为从焦油坑中整理出可测试的接口，给他添加测试，以此作为继续重构的立足点。
* 记住所有软件的“坏味道”，以及对应的重构手法，记住常见的重构步骤。
* 添加新功能前先重构

## 保证重构的效果

* 熟悉常用的重构的手法
* 统一编码规范
* 测试
* Code Review
* 选用强大的重构工具，如 eclipse
 

## 何时重构

* 随时。重构一种开发的习惯
* “事不过三，三则重构”
* 添加新功能时
* 修改错误时
* Code Review

## 重构的难题

* 数据层（数据模型）的变更压力
* 修改接口
* 那些难以通过重构改变的设计改动
* 项目期限压力（何时不该重构）。项目后期，或者打算重写
* 代码不能运行
 

## 重构 VS 设计

一般的编码有如下方式：

* 预先设计->编码
* 编码-> 重构
* 预先设计->编码-> 重构

编程不是机械的开发，而是艺术行为！设计和重构的要取得平衡（预先设计的难度和重构灵活性的平衡）

## 重构 VS 性能

* 重构并不对性能做优化，有时可能会让性能下降。但对后期优化的调整，会更加容易
* 提前优化是万恶之源

## 代码的坏味道

* DuplicatedCode（重复代码）：万恶之源
* LongMethod（过长函数）：会导致责任不明确/难以切割/难以理解等一系列问题
* LargeClass（过大的类）：职责不明确，垃圾滋生地
* LongParameterList（过长参数列）：没有面向对象
* DivergentChange（发散式变化）：一个类会响应多种需求而被修改
* ShotgunSurgery（霰弹式修改）：其实就是没有封装变化处，由于一个需求，多处需要被修改
* FeatureEnvy（依恋情结）：一个类对其他类过多的依赖
* DataClumps（数据泥团）：如果数据有意义，就将结构数据变成对象
* PrimitiveObsession（基本类型偏执）：使用 Class 替代
* SwitchStatements（switch惊悚现身）：少用 switch，考虑多态
* ParallelInheritanceHierarchies（平行继承体系）：过多平行的类，使用类继承并联起来
* LazyClass（冗赘类）：去除冗余
* SpeculativeGenerality（夸夸其谈未来性）：扯了太多没用的淡
* TemporaryField（令人迷惑的暂时字段）：封装它
* MessageChains（过度耦合的消息链）：使用真正需要的函数和对象，而不要依赖于消息链
* MiddleMan（中间人）：过度代理
* InappropriateIntimacy（狎昵关系）：过度使用其他类private值域
* AlternativeClasseswithDifferentInterfaces（异曲同工的类）：重复作用的类，合并成一个类
* IncompleteLibraryClass（不完美的库类）：引入外部方法
* DataClass（纯稚的数据类）
* RefusedBequest（被拒绝的遗赠）
* Comments（过多的注释）：写注释前先重构，去掉多余的注释。注释多了，就说明代码不清楚了

## 测试体系

* 每个类都要有测试
* 自动化测试，自动检查测试结果
* 编写功能前先写测试代码
* 使用 JUnit 进行单元测试
* 编写测试时，先让测试失败，以检验测试机制可行
* 测试是风险驱动的，测试现在或者未来可能出现的错误。“编写未臻完善的测试并实际运行，好过完美测试的无尽等待”
* 考虑可能出错的边界条件
* “花合理的时间抓出大多数bug”好过“穷尽一生抓出所有的bug”

## 重构常用手法

### 重新组织函数

* ExtractMethod（提炼函数）110
* InlineMethod（内联函数）117
* InlineTemp（内联临时变量）119
* ReplaceTempwithQuery（以查询取代临时变量）120
* IntroduceExplainingVariable（引入解释性变量）124
* SplitTemporaryVariable（分解临时变量）128
* RemoveAssignmentstoParameters（移除对参数的赋值）131
* ReplaceMethodwithMethodObject（以函数对象取代函数）135
* SubstituteAlgorithm（替换算法）139

### 在对象之间搬移特性

* MoveMethod（搬移函数）142
* MoveField（搬移字段）146
* ExtractClass（提炼类）149
* InlineClass（将类内联化）154
* HideDelegate（隐藏“委托关系”）157
* RemoveMiddleMan（移除中间人）160
* IntroduceForeignMethod（引入外加函数）162
* IntroduceLocalExtension（引入本地扩展）164

### 重新组织数据

* SelfEncapsulateField（自封装字段）171
* ReplaceDataValuewithObject（以对象取代数据值）175
* ChangeValuetoReference（将值对象改为引用对象）179
* ChangeReferencetoValue（将引用对象改为值对象）183
* ReplaceArraywithObject（以对象取代数组）186
* DuplicateObservedData（复制“被监视数据”）189
* ChangeUnidirectionalAssociationtoBidirectional（将单向关联改为双向关联）197
* ChangeBidirectionalAssociationtoUnidirectional（将双向关联改为单向关联）200
* ReplaceMagicNumberwithSymbolicConstant（以字面常量取代魔法数）204
* EncapsulateField（封装字段）206
* EncapsulateCollection（封装集合）208
* ReplaceRecordwithDataClass（以数据类取代记录）217
* ReplaceTypeCodewithClass（以类取代类型码）218
* ReplaceTypeCodewithSubclasses（以子类取代类型码）223
* ReplaceTypeCodewithState/Strategy（以State/Strategy取代类型码）227
* ReplaceSubclasswithFields（以字段取代子类）232

### 简化条件表达式

* DecomposeConditional（分解条件表达式）238
* ConsolidateConditionalExpression（合并条件表达式）240
* ConsolidateDuplicateConditionalFragments（合并重复的条件片段）243
* RemoveControlFlag（移除控制标记）245
* ReplaceNestedConditionalwithGuardClauses（以卫语句取代嵌套条件表达式）250
* ReplaceConditionalwithPolymorphism（以多态取代条件表达式）255
* IntroduceNullObject（引入Null对象）260
* IntroduceAssertion（引入断言）267

### 简化函数调用

* RenameMethod（函数改名）273
* AddParameter（添加参数）275
* RemoveParameter（移除参数）277
* SeparateQueryfromModifier（将查询函数和修改函数分离）279
* ParameterizeMethod（令函数携带参数）283
* ReplaceParameterwithExplicitMethods（以明确函数取代参数）285
* PreserveWholeObject（保持对象完整）288
* ReplaceParameterwithMethods（以函数取代参数）292
* IntroduceParameterObject（引入参数对象）295
* RemoveSettingMethod（移除设值函数）300
* HideMethod（隐藏函数）303
* ReplaceConstructorwithFactoryMethod（以工厂函数取代构造函数）304
* EncapsulateDowncast（封装向下转型）308
* ReplaceErrorCodewithException（以异常取代错误码）310
* ReplaceExceptionwithTest（以测试取代异常）315

### 处理概括关系

* PullUpField（字段上移）320
* PullUpMethod（函数上移）322
* PullUpConstructorBody（构造函数本体上移）325
* PushDownMethod（函数下移）328
* PushDownField（字段下移）329
* ExtractSubclass（提炼子类）330
* ExtractSuperclass（提炼超类）336
* ExtractInterface（提炼接口）341
* CollapseHierarchy （折叠继承体系）344
* FormTemPlateMethod （塑造模版函数）345
* ReplaceInheritanceWithDelegation （以委托取代继承）352
* ReplaceDelegationWithInheritance （以继承取代委托）355

## 大型重构

* TeaseApartInheritance （梳理并分解继承体系） 362
* ConvertPrceduralDesignToObjects （将过程化设计转化为对象设计）368
* SeparateDomainFromPresentation （将领域和表述/显示分离）370
* ExtractHierarchy （提炼继承体系）375

## 重构的本质

* 消除重复代码
* 减少结构的体积
* 更加 OO
* 让代码易读、易懂、易维护

## 重构的阻力

* 不知道如何重构
* 缺乏重构的动力。只看到眼前的利益，并不关心项目的未来
* 进度的压力
* 老板、客户更加关注新功能，看不到重构的价值
* 重构有破坏现有程序的风险

## 如何学习重构

* 随时进行重构。当发现某处代码发出“坏味道”，就要马上解决
* 没有把握就停下来。如果代码改善了就发布，如果没有就撤回
* 学习原路返回。回到没有出错的状态，再前进重构，重构完都要测试