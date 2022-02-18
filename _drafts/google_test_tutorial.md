---
layout: post
title: 使用 gtest 测试框架
categories: unittest
description: google_test 个人使用总结
keywords: gtest, unittest
---

#单元测试简介

##一、什么是单元测试

在计算机编程中，单元测试是一种软件测试方法，通过该方法可以测试源代码的各个单元以确定它们是否适合使用。 单元是最小的可测试软件组件， 它通常执行单个内聚功能。单元测试就是是指对这个最小可测试组件——即单元进行检查和验证。

单元体量小，因此比大块代码更容易设计、执行、记录和分析测试结果。 通过单元测试发现的缺陷很容易定位，并且相对容易修复。单元测试的目标是将程序分离成各自独立的部分，并测试各个部分是否正常工作。它将可测试软件的最小部分与代码的其余部分隔离开来，并确定其行为是否与预期的完全一致。单元测试能在使用过程中发现很多缺陷，在这种过程中证明自身价值。**它实现了测试过程的自动化，减少了发现应用程序中更复杂部分中包含的错误的困难，并且由于可以关注到每一个单元而提高测试覆盖率。**

**单元测试的优势**

* 使流程更灵活：单元测试有助于安全重构
* 保证代码质量：暴露出极端情况，让人编写出质量更高的代码
* 尽早发现软件Bug：让问题在早期就被识别发现
* 促进变化并简化集成：减少新开发功能中的缺陷，减少现有功能更改时出现的错误
* 提供文档：提供系统的文档，有助于了解相关功能及如何使用
* 简化调试过程：没有冗长的调试过程，只有关注测试的功能点
* 设计：使用测试驱动开发，驱动更松耦合的代码设计和实现
* 降低成本：降低错误修复的成本

**总结**

单元测试是针对代码单元的独立测试，核心是“独立”，优势来源也是这种独立性，而所面临的不足也正是因为其独立性：既然是“独立”，就难以测试与其他代码和依赖环境的相互关系。 单元测试与系统测试是互补而非代替关系。单元测试的优势，正是系统测试的不足，单元测试的不足，又恰是系统测试的优势。不能将单元测试当做解决所有问题的万金油，而需理解其优势与不足，扬长避短，与系统测试相辅相成，实现测试的最大效益

参考：[https://dzone.com/articles/top-8-benefits-of-unit-testing](https://dzone.com/articles/top-8-benefits-of-unit-testing)

##二、C++ 单元测试框架选型

**主流C/C++测试框架对比**

| **测试框架特性** | **Boost Test** | **CppUnit** | **Gtest** | **TestNgpp** |
|------------------|----------------|-------------|-----------|---------------|
| 是否开源         | 是             | 是          | 是        | 是            |
| 自动检测注册     | 良             | 差          | 优        | 优            |
| 断言能力         | 良             | 较弱        | 优        | 优            |
| 支持Fixture      | 支持           | 支持        | 支持      | 支持          |
| 支持Suite分组    | 支持           | 支持        | 支持      | 支持          |
| 支持用例过滤     | 支持           | 支持        | 支持      | 支持          |
| 测试报表         | 不支持         | 支持        | 支持      | 支持          |
| 测试能力         | 良             | 良          | 优        | 优            |
| 用例依赖管理     | 不支持         | 不支持      | 不支持    | 支持          |

其中TestNgpp功能虽然最强大，但是用户较少。Google推出的Gtest框架使用范围最广，社区支持程度也最好，从功能上来说简单易用，作为上手框架最为合适。其它框架由于各种缺陷不建议再选用了。

**Mock框架**

对于C++语言来说，主要有两款易用的mock框架：gmock和mockcpp。待扩展。

#google test

官方手册地址：[https://google.github.io/googletest/](https://google.github.io/googletest/)

参考文章：[玩转Google开源C++单元测试框架Google Test系列(gtest)(总)](https://www.cnblogs.com/coderzh/archive/2009/04/06/1426755.html)

##一、google test 官方手册中文翻译

**GoogleTest 用户手册**

欢迎来到GoogleTest！

GoogleTest 是谷歌的 C++ 测试及模拟框架。用户指导有以下内容：

* [GoogleTest 基础](https://google.github.io/googletest/primer.html)
  -教你如何用GoogleTest编写简单的测试，新手优先阅读。 [中文翻译](./google_test_primer_chinese.md)
* [GoogleTest 进阶](https://google.github.io/googletest/advanced.html)
  -阅读此指导手册，助你发掘GoogleTest的所有潜能
* [GoogleTest 样例](https://google.github.io/googletest/samples.html)
  -GoogleTest的一些应用样例及描述
* [GoogleTest 问答](https://google.github.io/googletest/faq.html)
  -遇到问题了？想要获取一些提示？来这看看吧^-^
* [傻瓜 Mocking 教程](https://google.github.io/googletest/gmock_for_dummies.html)
  -教你如何创建一个mock对象并在测试中使用它们
* [Mocking 食谱](https://google.github.io/googletest/gmock_cook_book.html)
  -包含常见mock用例的使用提示和方法
* [Mocking 备忘录](https://google.github.io/googletest/gmock_cheat_sheet.html)
  -matchers, actions, invariants等便捷参考
* [Mocking 问答](https://google.github.io/googletest/gmock_faq.html)
  -包含一些特定mocking问题的回答

