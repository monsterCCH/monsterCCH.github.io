---
layout: wiki
title: yaml tutorial
categories: yaml
description: yaml 语言的使用教程
keywords: yaml
---

## 简介

YAML (YAML Ain't a Markup Language) YAML 不是一中标记语言的递归缩写，实际上应该是 "Yet Another Markup Language", 仍是一种标记语言。

YAML 语言的设计目标就是以一种通用的数据串行化格式方便用户读写。

## 基本语法

* 大小写敏感
* 使用锁进表示层级关系
* 缩进不允许使用 tab,只允许使用空格
* 缩进的空格数不重要，只要相同层级的元素左对齐即可
* '#' 表示注释

## 数据类型

* 对象：键值对的集合，又称为映射（mapping）/ 哈希（hashes） / 字典（dictionary）
* 数组：一组按次序排列的值，又称为序列（sequence） / 列表（list）
* 纯量（scalars）：单个的、不可再分的值

### 对象

#### 语法格式

```yaml
# 对象键值对使用冒号结构, 冒号后加空格
key: value
等价于
key_g: {key1: value1, key2: value2, ...}

# 较为复杂的对象格式，可以使用问号加一个空格代表一个复杂的 key，
# 配合一个冒号加一个空格代表一个 value：
# 意思即对象的属性是一个数组 [complexkey1,complexkey2]，
# 对应的值也是一个数组 [complexvalue1,complexvalue2]

?
  - complexkey1
  - complexkey2
:
  - complexvalue1
  - complexvalue2
```

### 数组

#### 语法格式

```yaml
# 以 - 开头的行表示构成一个数组：
- A
- B
- C

# 行内表示
key: [value1, value2, ...]

# 数据结构的子成员是一个数组，则可以在该项下面缩进一个空格。
-
 - A
 - B
 - C

# demo
companies:
  -
    id: 1
    name: company1
    price: 200W
  -
    id: 2
    name: company2
    price: 500W

# 意思是 companies 属性是一个数组，
# 每一个数组元素又是由 id、name、price 三个属性构成。
# 等价于
companies: [{id: 1,name: company1,price: 200W},{id: 2,name: company2,price: 500W}]
```

### 复合结构

```yaml
languages:
  - Ruby
  - Perl
  - Python 
websites:
  YAML: yaml.org 
  Ruby: ruby-lang.org 
  Python: python.org 
  Perl: use.perl.org
```
等价于

```json
{ 
  "languages": [ "Ruby", "Perl", "Python"],
  "websites": {
    "YAML": "yaml.org",
    "Ruby": "ruby-lang.org",
    "Python": "python.org",
    "Perl": "use.perl.org" 
  } 
}
```

### 纯量

纯量是最基本的、不可再分的值。

* 字符串
* 布尔值
* 整数
* 浮点数
* Null
* 时间
* 日期

demo:

```yaml
boolean: 
    - TRUE  #true,True都可以
    - FALSE  #false，False都可以
float:
    - 3.14
    - 6.8523015e+5  #可以使用科学计数法
int:
    - 123 #字符串默认不使用引号
    - 0b1010_0111_0100_1010_1110    #二进制表示
null:
    nodeName: 'node'
    parent: ~  #使用~表示null
string:
    - 哈哈
    - 'Hello world'  #可以使用双引号或者单引号包裹特殊字符
                    #单引号和双引号都可以使用，双引号不会对特殊字符转义
                    #单引号之中如果还有单引号，必须连续使用两个单引号转义
    - newline
      newline2    #字符串可以拆成多行，每一行会被转化成一个空格
    - |+      #+表示保留文字块末尾的换行，-表示删除字符串末尾的换行
      Foo
      Bar
    - >
      Foo
      Bar   #多行字符串可以使用|保留换行符，也可以使用>折叠换行。              
date:
    - 2018-02-17    #日期必须使用ISO 8601格式，即yyyy-MM-dd
datetime: 
    -  2018-02-17T15:02:31+08:00    #时间使用ISO 8601格式，时间和日期之间使用T连接，最后使用+代表时区
type_convert:
    -!!str 123 # 使用两个感叹号，强制转换数据类型
```

### 引用

& 锚点和 * 别名，可以用来引用

```yaml
defaults: &defaults
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  <<: *defaults

test:
  database: myapp_test
  <<: *defaults
```
等价于
```yaml

defaults:
  adapter:  postgres
  host:     localhost

development:
  database: myapp_development
  adapter:  postgres
  host:     localhost

test:
  database: myapp_test
  adapter:  postgres
  host:     localhost
```