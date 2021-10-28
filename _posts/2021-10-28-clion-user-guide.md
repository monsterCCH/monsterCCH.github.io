---
layout: post
title: JetBrains IDE 系列之CLION  
categories: IDE
description: C/C++ 集成开发环境 CLION 的配置使用说明
keywords: CLION, IDE, GIT
---
# 工具获取

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


# 环境配置
***

## IDE 内存大小设置

由于 clion 运行在 Java 虚拟机上，所以内存的分配直接影响到使用的流畅度，安装后默认的 Java 运行堆栈内存大小为2048M，
这对于一些中大型项目以及同时开启多个项目的情况就有点捉襟见肘了，就本人使用体验来说，建议内存分配4G以上，机器资源充足的情况也可多多益善。

**修改方法:**

打开Help->Custom VM Options
或者Help->Change Memory Setting

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

## IDE 参数设置
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

## IDE 开发环境设置
***

为了在 IDE 上直接编码编译调试，还需要设置编译工具链，CLION 提供了多种编译链工具，在这选择 Remote Host 方式, Remote Host 使用 SSH 方式连接
，配置好相应的 SSH 连接参数及编译工具地址即可

![](/images/posts/dev_tools/clion_setting_toolchains.jpg)

***
CMAKE 编译设置，可以设置 CMAKE 项目编译类型，选择已经设置好的编译工具链，编译线程数可根据运行环境自行设置，建议取消CMakeLists变动重新加载，
项目较大时重新加载一次比较耗时。

![](/images/posts/dev_tools/clion_setting_cmake.jpg)

***
设置远端开发环境，默认使用 sftp 协议连接，可设置到远端的文件目录的映射

![](/images/posts/dev_tools/clion_setting_develop.jpg)

File | Settings | Build, Execution, Deployment | Deployment | Options 配置文件传输选项

![](/images/posts/dev_tools/clion_setting_develop_option.jpg)

勾选自动上传默认服务器及本地删除文件使删除远端文件，可以防止文件重名时远端环境产生多余文件。

***
File | Settings | Build, Execution, Deployment | Dynamic Analysis Tools 

CLION 内部集成了Valgrind、Sanitizers、Perf 动态分析工具以提供代码分析

# 使用技巧
***

* **TODO**

编码中含有待处理事项时，添加注释 *//TODO*，即可在下方工具栏查看待处理事项。

* **添加书签**

Navigate | Bookmarks
为了快速找到代码，可以在代码中添加书签，默认添加书签快捷键 CTRL+K+K, 重复取消，CTRL+` 查看书签。

* **查看收藏**

侧边工具 Favorites,点击可查看项目所有收藏的文件 *[File | Add to Favorites 添加当前编辑文件到我的收藏]*、书签以及断点 *[添加 debug 断点点击编辑窗口页码后即可]*。

* **查看粘贴板**

CTRL+SHIFT+V 即可查看最近复制内容，选择内容可快速重复复制。

* **查看最近操作**

CTRL+, 当打开文件过多，上方文件导航窗口不够用时， 可查看最近打开文件，快速进行文件切换。

CTRL+SHIFT+, 查看最近修改文件

CTRL+SHIFT+E 查看最近定位位置

* **查看修改历史**

File | Local History | show history 可以查看 IDE 的修改历史，如在 IDE 中不小心删除文件，可以在历史记录中还原修复 

* **编辑操作**

CTRL+F 查看当前文件内容，支持忽略大小写和正则匹配，以及查找范围过滤，如过滤注释、字符串

CTRL+H 批量替换查找文件内容

CTRL+SHIFT+F 全局搜索内容，可选择搜索范围，包括自定义的文件范围

CTRL+SHIFT+H 全局搜索替换

ALT+SHIFT+INSERT 切换内容选择方式，类似于 vim 的 CTRL+V

ALT+SHIFT+G 光标快速移动到行结尾

ALT+SHIFT+= 从光标处依次扩展选择内容

ALT+SHIFT+- 与 ALT+SHIFT+= 相反，依次缩小选择范围

CTRL+SHIFT+U 切换大小写

CTRL+D 复制行

CTRL+L 删除行

Tab 增加缩进 

Shift+Tab 减小缩进 建议修改缩进字符为4个空格

* **文件比较**

在左侧项目窗口选中目标对象后，使用 CTRL+D 可与本机文件进行文件对比，在不同版本管理下，如 git 和 svn 版本对比中比较有用

* **查找一切**

双击SHIFT, 在弹出的窗口中可以查找你能想到的一切东西，包含 IDE 内部操作，class、 files、 symbols、 git 分支及提交记录，甚至你可以在弹出的窗口中直接进行简单的数值计算

* **快速导航**

CTRL+G 快速定位行号

CTRL+SHIFT+[ 快速切换不同项目 

CTRL+SHIFT+BACKSPACE 快速定位到上次修改点

ALT+O 快速切换源码头文件

ALT+上下方向键 快速切换上下函数

ALT+F1 常用操作为 Project View 快速定位当前文件在项目文件中的位置
    
F12 跳转函数定义

ALT+F12 以弹窗形式展示函数定义，不跳转文件

ALT+END 在 C++ 中，虚函数无法使用 F12 直接跳转，使用此快捷键可以查找所有实现

CTRL+- 回退跳转

CTRL+SHIFT+- 复位跳转

ATL+\ 展示当前文件所有函数 也可在侧边栏 Structure 中查看

* **代码格式化**

选中内容后 CTRL+ARL+ENTER


# CLION 版本控制 GIT 使用

* **GIT 安装**

Windows 环境下安装GIT windows版，设置好相应的 GIT 配置

* **项目拉取**

使用 git clone 拉取 git项目文件

![](/images/posts/dev_tools/clion_git_clone.jpg)

* **创建分支**

点击 git 功能，选中相应的分支右键即可基于选中的分支创建自己的开发分支，在此分支上的所有修改必须提交后推送到 GIT 远程分支，再发起 merge request，经过管理员同意后才能合并到相应分支

![](/images/posts/dev_tools/clion_git_branch.jpg) 

* **查看历史修改记录** 

双击指定分支后，会显示当前分支的历史提交记录，提交记录窗口提供信息检索过滤，可查找提交信息关键字，过滤提交用户、时间，再选中相应的历史记录，会在最右侧展示该次提交修改的文件内容，双击文件即可
查看修改内容对比

![](/images/posts/dev_tools/clion_git_history.jpg) 

***

![](/images/posts/dev_tools/clion_compare_info.jpg)

如果 GIT 列表中没有 PUSH 过的远程分支，使用左下斜箭头去更新远端分支到本地

* **GIT 版本对比**

在项目列表选中目标，右键选择 GIT->Compare with Branch 可对比不同版本分支内容差异

* **GIT commit**

点击提交按钮，弹出的窗口中会展示所有改动记录，包括文件的增删改动，是否纳入版本控制等信息，双击文件即可查看改动内容，如发现错误改动，
可在对比页面还原内容或者右键选择 Rollback 回滚整个文件改动。此页面可选择需要提交的文件内容，也可将不同的改动文件放入不同的 ChangeList，
方便文件提交分类管理。此处有一个比较常用的选项 Amend commit，勾选此项的场景主要在针对一个功能点改动时，一次修改未能完全修复，
需要多次提交的情况下，每次提交会重用上次的提交记录，这样历史提交记录中只会有一次，方便查看改动内容。

![](/images/posts/dev_tools/clion_git_commit.jpg) 

* **修改 commit**

如果提交后想要修改提交信息，右键提交记录，选择 Edit Commit Message 即可修改提交信息 

如果提交后发现提交错误，但又不想保留修改内容，选择Drop commit, 慎用，本地修改内容会消失

如果想单纯的撤销提交，但想要保留改动内容，在提交的上一次提交记录处右键 选择 Reset Current Branch to Here, 注意选择 Soft 方式。

* **更新本地分支**

如果有多人在同一个基础分支上开发，他人的提交被更新到基础分支，为了提早发现改动内容有冲突，使用 GIT->Update Project 更新本地分支，
更新方式有两种，merge 和 rebase, 实现效果是一样的，但建议使用 rebase。两者区别如下：

    git merge优点是分支代码合并后不破坏原分支的代码提交记录，缺点就是会产生额外的提交记录并进行两条分支的合并，

    git rebase 优点是无须新增提交记录到目标分支，rebase后可以将对象分支的提交历史续上目标分支上，形成线性提交历史记录，进行review的时候更加直观

* **同步不同版本修改** 

有时需要同步不同版本之间修改，在 git branch 中选择想要同步的分支修改记录，右键选择Cherry-pick, 即可将别的版本的修改内容同步到当前开发分支，
此过程可能会产生代码冲突，解决代码冲突即可。

* **分支提交**

直接 PUSH，冲冲冲！