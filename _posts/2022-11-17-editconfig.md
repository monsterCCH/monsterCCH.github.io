---
layout: post
title: 文件格式统一工具 editorconfig
categories: format
description: 文件格式统一工具 editorconfig
keywords: editorconfig
---

## 介绍
在多人协作开发项目中，不同的开发人员可能会偏好不同的开发工具，即使是同一IDE也可能有着不同IDE设置，比如默认的换行符、字符编码不一致等。editorconfig就是为了帮助开发团队保持一致的编码风格。editorconfig 项目包含用于定义编码样式的文件格式和一组文本编辑器插件，使编辑器能够读取文件格式并遵守定义的样式。editorconfig 文件易于阅读，并且可以很好地与版本控制系统配合使用。为了保持代码风格的统一，类似的还有clang-format，但 clang-format 主要是为了控制编码规范，和编程语言关联性比较强，editorconfig则主要是文件层面的规范。

## 使用说明
在项目根目录下添加`.editorconfig`文件，许多开源项目就是如此，官网配置样例如下
```.editorconfig
# EditorConfig is awesome: https://EditorConfig.org

# 表示是最顶层的配置文件，发现设为true时，才会停止查找.editorconfig文件
# top-most EditorConfig file
root = true

# Unix-style newlines with a newline ending every file
[*]
end_of_line = lf # 设置为lf、cr或crlf以控制换行符的表示方式。
insert_final_newline = true # 设为true表示使文件以一个空白行结尾

# Matches multiple files with brace expansion notation
# Set default charset
[*.{js,py}]
charset = utf-8 # 设置编码

# 4 space indentation
[*.py]
indent_style = space # 设置缩进风格
indent_size = 4 # 设置缩进的宽度

# Tab indentation (no size specified)
[Makefile] #指定文件
indent_style = tab

# Indentation override for all JS under lib directory
[lib/**.js]
indent_style = space
indent_size = 2

# Matches the exact files either package.json or .travis.yml
[{package.json,.travis.yml}]
indent_style = space
indent_size = 2

```

### 语法

editorconfig配置文件需要以UTF-8字符集编码, 以回车换行或换行作为一行的分隔符

'/'被用作为一个路径分隔符，'#'或';'被用作于注释. 注释需要与注释符号写在同一行

### 文件匹配
`[]`内指定匹配文件，语法如下
```editorconfig
*                匹配除/之外的任意字符串
**               匹配任意字符串
?                匹配任意单个字符
[name]           匹配name中的任意一个单一字符
[!name]          匹配不存在name中的任意一个单一字符
{s1,s2,s3}       匹配给定的字符串中的任意一个(用逗号分隔) 
{num1..num2}   　匹配num1到num2之间的任意一个整数, 这里的num1和num2可以为正整数也可以为负整数
```

### 可设置的属性
```editorconfig
indent_style    设置缩进风格(tab是硬缩进，space为软缩进)
indent_size     用一个整数定义的列数来设置缩进的宽度，如果indent_style为tab，则此属性默认为tab_width
tab_width       用一个整数来设置tab缩进的列数。默认是indent_size
end_of_line     设置换行符，值为lf、cr和crlf，一般Dos和Windows采用回车+换行(crlf)，UNIX和Linux采用换行符(lf)，MAC OS采用回车符(cr)来表示换行
charset         设置编码，值为latin1、utf-8、utf-8-bom、utf-16be和utf-16le，不建议使用utf-8-bom
trim_trailing_whitespace  设为true表示会去除换行行首的任意空白字符。
insert_final_newline      设为true表示使文件以一个空白行结尾
root        　　　表示是最顶层的配置文件，发现设为true时，才会停止查找.editorconfig文件
```

## 参考资料
[editorconfig官网](https://editorconfig.org/)

[统一代码风格工具——EditorConfig](https://cloud.tencent.com/developer/article/1546185)
