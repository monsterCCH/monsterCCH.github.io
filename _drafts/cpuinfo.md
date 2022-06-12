---
layout: post
title: /proc/cpuinfo 文件描述
categories: linux
description: /proc/cpuinfo 文件描述
keywords: linux, cpuinfo
---

## cat /proc/cpuinfo

```text
processor       : 5
vendor_id       : AuthenticAMD
cpu family      : 25
model           : 33
model name      : AMD Ryzen 7 5800X 8-Core Processor
stepping        : 0
microcode       : 0xffffffff
cpu MHz         : 4200.042
cache size      : 512 KB
physical id     : 2
siblings        : 2
core id         : 1
cpu cores       : 2
apicid          : 5
initial apicid  : 5
fpu             : yes
fpu_exception   : yes
cpuid level     : 13
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw topoext ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 xsaves clzero arat umip vaes vpclmulqdq rdpid overflow_recov succor
bugs            : fxsave_leak sysret_ss_attrs null_seg spectre_v1 spectre_v2 spec_store_bypass
bogomips        : 8400.08
TLB size        : 2560 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 45 bits physical, 48 bits virtual
power management:

processor       : 6
vendor_id       : AuthenticAMD
cpu family      : 25
model           : 33
model name      : AMD Ryzen 7 5800X 8-Core Processor
stepping        : 0
microcode       : 0xffffffff
cpu MHz         : 4200.042
cache size      : 512 KB
physical id     : 3
siblings        : 2
core id         : 0
cpu cores       : 2
apicid          : 6
initial apicid  : 6
fpu             : yes
fpu_exception   : yes
cpuid level     : 13
wp              : yes
flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw topoext ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 xsaves clzero arat umip vaes vpclmulqdq rdpid overflow_recov succor
bugs            : fxsave_leak sysret_ss_attrs null_seg spectre_v1 spectre_v2 spec_store_bypass
bogomips        : 8400.08
TLB size        : 2560 4K pages
clflush size    : 64
cache_alignment : 64
address sizes   : 45 bits physical, 48 bits virtual
power management:

```

## 输出信息解释

| key              | value                                                      | description                                                                                                        |
| :----            | :----                                                      | :----                                                                                                              |
| processor        | 6                                                          | 系统中逻辑处理核的编号                                                                                  |
| vendor_id        | AuthenticAMD                                               | CPU 制造商                                                                                                      |
| cpu family       | 25                                                         | CPU 产品系列代号                                                                                             |
| model            | 33                                                         | CPU 属于其系列中的哪一代的代号                                                                        |
| model name       | AMD Ryzen 7 5800X 8-Core Processor                         | CPU属于的名字及其编号                                                                                     |
| stepping         | 0                                                          | 步进编号用来标识处理器的设计或制作版本                                                          |
| microcode        | 0xffffffff                                                 | CPU 补丁微码                                                                                                   |
| cpu MHz          | 4200.042                                                   | CPU 主频                                                                                                         |
| cache size       | 512 KB                                                     | CPU 二级缓存大小                                                                                             |
| physical id      | 3                                                          | 每个物理封装的唯一标识符,即物理 CPU 的标识符，CPU个数等于 physical id 的最大值加1     |
| siblings         | 2                                                          | 位于相同物理封装中的逻辑处理器的数量,即每个物理CPU包含2个逻辑 CPU                      |
| core id          | 0                                                          | 当前物理核在其所处 CPU 中的编号，这个编号不一定连续                                         |
| cpu cores        | 2                                                          | 位于相同物理封装中的内核数量                                                                         |
| apicid           | 6                                                          | 用来区分不同逻辑核的编号，系统中每个逻辑核的此编号必然不同，此编号不一定连续 |
| initial apicid   | 6                                                          | 大部分情况同 apicid                                                                                          |
| fpu              | yes                                                        | 是否具有浮点运算单元（Floating Point Unit）                                                            |
| fpu_exception    | yes                                                        | 是否支持浮点计算异常                                                                                     |
| cpuid level      | 13                                                         | 执行 cpuid 指令前，eax 寄存器中的值，根据不同的值 cpuid 指令会返回不同的内容          |
| wp               | yes                                                        | 表明当前 CPU 是否在内核态支持对用户空间的写保护(Write Protection)                             |
| flags            | fpu vme de pse tsc msr pae mce  cx16 sse4_1 sse4_2 ...     | 当前CPU支持的功能                                                                                           |
| bugs             | fxsave_leak sysret_ss_attrs null_seg spectre_v1 spectre_v2 | CPU 已知的硬件漏洞                                                                                          |
|                  | spec_store_bypass                                          |                                                                                                                    |
| bogomips         | 8400.08                                                    | 在系统内核启动时粗略测算的 CPU 速度(Million Instructions Per Second)                                |
| TLB size         | 2560 4K pages                                              | (translation lookaside buffer) 高速缓存大小                                                                  |
| clflush size     | 64                                                         | 每次刷新缓存的大小单位                                                                                  |
| cache_alignment  | 64                                                         | 缓存地址对齐单位                                                                                           |
| address sizes    | 45 bits physical, 48 bits virtual                          | 可访问地址空间位数                                                                                        |
| power management |                                                            | 对能源管理的支持                                                                                           |

## 查看 cpu 核数

```shell
# 查看物理 cpu 个数
cat /proc/cpuinfo | grep "physical id" | sort | uniq |wc -l

# 查看每个物理 cpu 封装核数
cat /proc/cpuinfo| grep "cpu cores" | uniq

# 查看逻辑CPU的个数
cat /proc/cpuinfo| grep "processor"| wc -l

# 查询系统的每颗物理CPU核心是否启用超线程技术，如果cpu cores数量和siblings数量一致，则没有启用超线程，否则超线程被启用。
cat /proc/cpuinfo | grep -e "cpu cores"  -e "siblings" | sort | uniq
```

逻辑CPU=物理CPU个数×CPU核数，如果不相等的话，则表示服务器的CPU支持超线程技术。