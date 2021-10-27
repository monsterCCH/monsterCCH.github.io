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
***

##IDE 内存大小设置

由于 clion 运行在 Java 虚拟机上，所以内存的分配直接影响到使用的流畅度，安装后默认的 Java 运行堆栈内存大小为2048M，
这对于一些中大型项目以及同时开启多个项目的情况就有点捉襟见肘了，就本人使用体验来说，建议内存分配4G以上，机器资源充足的情况也可多多益善。

**修改方法:**

打开Help->Custom VM Options

```
# custom CLion VM options

-Xmx8192m **此处修改vm内存大小**
-Xms256m
-XX:ReservedCodeCacheSize=512m
-Xss2m
-XX:NewSize=128m
-XX:MaxNewSize=128m
-XX:+UseG1GC
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:CICompilerCount=2
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-ea
-Dsun.io.useCanonCaches=false
-Djdk.http.auth.tunneling.disabledSchemes=""
-Djdk.attach.allowAttachSelf=true
-Djdk.module.illegalAccess.silent=true
-Dkotlinx.coroutines.debug=off
-Dconsole.encoding=UTF-8
-Dfile.encoding=UTF-8
```

也可根据实际使用情况调整，实际使用情况可查看右下角动态显示

![](/images/posts/dev_tools/clion_vmmem.jpg)

***
如右下角没有展示，可右键底部标签栏选择想要展示的功能

![](/images/posts/dev_tools/clion_display_item_down.jpg)

##IDE 参数设置
***
外观主题设置可根据喜好自行设置，界面UI设置本人设置如下：

![](/images/posts/dev_tools/clion_setting_appearance.jpg)

***
项目文件范围设置，可自定义文件范围，方便项目搜索时自定义搜索范围

![](/images/posts/dev_tools/clion_setting-scope.jpg)

***
File | Settings | Appearance & Behavior | Quick Lists 自定义操作列表，可以添加常用的操作到列表中并在Keymap中添加快捷键 

***
快捷键设置，如果之前熟悉别的 IDE 默认快捷键，可以选择相应的默认配置，本人使用的是 Visual Studio 主题，也可根据自己需求自定义快捷键

![](/images/posts/dev_tools/clion_setting_keymap.jpg)

***
代码风格设置，可设置文件换行描述符，unix 下程序建议使用 ‘/n’ 结尾，防止文件出现 '^M' 字符，为防止一行过长，增强可阅读性，也可设置自动换行。
如果有代码风格统一的需求，可以启用 ClangFormat, IDE 会读取 .clang-format 配置的代码格式 

![](/images/posts/dev_tools/clion_setting_codeStyle.jpg)

***
代码检查设置，IDE 集成的代码检查工具，可检查代码的语法错误，提示问题并给出改进建议，提高编码时的准确性，默认配置即可，对于一些可忽略的问题，
可在此修改响应语言的检查项及提示方式。

![](/images/posts/dev_tools/clion_setting_inspections.jpg)

右上角展示代码检查结果统计，点击可展示详细提示信息

![](/images/posts/dev_tools/clion_setting_code_check.jpg)

***
File | Settings | Editor | File Encodings 文件编码设置，根据不同的项目设置统一的文件编码

***
实时模板代码生成，提高编码效率，输出关键字如 for 后，敲击 tab 键后会生成一个 for 循环语句模板，只需输出变量即可，支持自定义代码模板。

![](/images/posts/dev_tools/clion_setting_live_temp.jpg)

***
指定文件类型，有些文件类型没有在 CLION 的映射文件类型中，如以 .conf 结尾的文件在 CLION 中可能无法打开，在相应的文件类型映射中添加匹配项即可

![](/images/posts/dev_tools/clion_setting_file_type.jpg)

##IDE 开发环境设置
***

为了在 IDE 上直接编码编译调试，还需要设置编译工具链，CLION 提供了多种编译链工具，在这选择 Remote Host 方式, Remote Host 使用 SSH 方式连接
，配置好相应的 SSH 连接参数及编译工具地址即可

![](/images/posts/dev_tools/clion_setting_toolchains.jpg)




#使用技巧
***

* **TODO**

编码中含有待处理事项时，添加注释 *//TODO*，即可在下方工具栏查看待处理事项。

* **添加书签**

Navigate | Bookmarks
为了快速找到代码，可以在代码中添加书签，默认添加书签快捷键 CTRL+K+K, 重复取消，CTRL+` 查看书签。

* **查看收藏**

侧边工具 Favorites,点击可查看项目所有书签以及断点 *[添加 debug 断点点击编辑窗口页码后即可]*。

* **查看粘贴板**

CTRL+SHIFT+V 即可查看最近复制内容，选择内容可快速重复复制。

* **查看最近打开文件**

当打开文件过多，上方文件导航窗口不够用时，CTRL+, 可查看最近打开文件，快速进行文件切换。

* **查看修改历史**

File | Local History | show history 可以查看 IDE 的修改历史，如在 IDE 中不小心删除文件，可以在历史记录中还原修复 

#CLION 版本控制 GIT 使用

* **GIT 安装**

Windows 环境下安装GIT windows版，设置好相应的 GIT 配置

