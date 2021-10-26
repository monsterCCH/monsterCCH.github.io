---
layout: post
title: JetBrains IDE 系列之CLION  
categories: IDE
description: C/C++ 集成开发环境 CLION 的配置使用说明
keywords: CLION, IDE, GIT
---
#工具获取

CLION 官方下载地址：<https://www.jetbrains.com/clion/download>

软件适配支持 windows、linux、macos，首次安装可免费试用30天，建议支持正版。

CLION 作为一款 IDE ,本身支持集成的功能已经很丰富了，但同时也提供了丰富的插件扩展，可满足不同开发用户的各种需求。

网站下载地址：<https://plugins.jetbrains.com/clion> 

也可以在 File->Setting->Plugins->MarketPlace 中直接下载安装插件

以下列举一些本人使用的插件
* [One Dark theme](https://plugins.jetbrains.com/plugin/11938-one-dark-theme)
代码颜色主题插件，可根据个人喜好自行安装
* [Chinese Language Pack / 中文语言包](https://plugins.jetbrains.com/plugin/13710-chinese-simplified-language-pack----)
IDE 中文界面翻译插件，有需要可以安装，但中文翻译可能未完全覆盖，翻译也略显生硬，建议使用英文原版
* [IdeaVim](https://plugins.jetbrains.com/plugin/164-ideavim)
支持 vim 编辑操作，习惯 vim 码代码的可以安装
* [Json Parser](https://plugins.jetbrains.com/plugin/10650-json-parser)
Json 序列化工具，方便查看Json数据
* [IDE Eval Reset]()
神器插件，不提供下载

之前很多功能是以插件形式提供的，在新版 CLION 中都默认集成了，如数据库连接功能、markdown支持、shell脚本支持等。CLION 还集成了多个单元测试框架
，如boost.test、ctest、gtest等。所以使用2021版本的 CLION 如非特殊需要都可直接上手使用，如需特别的功能支持也可以去官方的插件市场寻找。


#环境配置



