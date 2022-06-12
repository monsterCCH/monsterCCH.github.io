---
layout: post
title: SSE 指令集加速
categories: cpu, sse, perf
description: SSE 指令集加速 
keywords: cpu, sse2, perf
---

## 什么是 sse

说到SSE，首先要弄清楚的一个概念是SIMD（单指令多数据流，Single Instruction Multiple Data），是一种数据并行技术，能够在一条指令中同时对多个数据执行运算操作，增加处理器的数据吞吐量。SIMD特别的适用于多媒体应用等数据密集型运算。

## sse 使用场景

### 字符替换加速

常用的字符替换写法

```c
char *pos = class_name;
size_t len = class_name_len;

while ((pos = (char *)memchr(pos, '\\', len - (pos - class_name)))) {
*pos++ = '_';
}
```

查看 cpu 是否支持 sse 指令集

```shell
cat /proc/cpuinfo | grep flags 

flags           : fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext pdpe1gb rdtscp lm constant_tsc rep_good nopl tsc_reliable nonstop_tsc cpuid extd_apicid pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt aes xsave avx f16c rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw topoext ibpb vmmcall fsgsbase bmi1 avx2 smep bmi2 erms invpcid rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 xsaves clzero arat umip vaes vpclmulqdq rdpid overflow_recov succor
```

本 CPU 支持 mmx, sse, sse2, ssse3, sse4.1, sse4.2，avx

使用 SIMD 128 指令集一次可以处理16个字符，替换代码如下

```c
char *pos = class_name;
    size_t len = class_name_len;
    const __m128i slash = _mm_set1_epi8('\\');
    const __m128i delta = _mm_set1_epi8('_' - '\\');

    while (len >= 16) {
        __m128i op = _mm_loadu_si128((__m128i *)pos);
        __m128i eq = _mm_cmpeq_epi8(op, slash);

        if (_mm_movemask_epi8(eq)) {
            eq = _mm_and_si128(eq, delta);
            op = _mm_add_epi8(op, eq);
            _mm_storeu_si128((__m128i*)pos, op);
        }
        len -= 16;
        pos += 16;
    }

    if (len) {
        replace_chr_normal(pos, len);
    }
```

1. __m128i eq = _mm_cmpeq_epi8(op, slash);
2. eq = _mm_and_si128(eq, delta);
3. op = _mm_add_epi8(op, eq);
4. _mm_storeu_si128((__m128i*)pos, op);

假设我们现在要处理的字符串是"G\Namespace\package\classname:

第一行：拿16个字符和字符'\\'做比较，如果某位相等，则16位结果中对应的byte就为0xff(-1), 否则就为0, 那么对于:

`G\Namespace\pack`

来说，会得到结果:

`0 -1 000000000 -1 0000`

第二行: 如果对比的结果不全为零的话，就进行到这行，这行的核心思想是因为ascii码’_’(95)和‘\\‘(92)之间差了3，所以我们通过and指令得到如下结果:

```
0 -1 000000000 -1 0000
&  3  3 333333333  3 3333
-------------------------
   0  3 000000000  3 0000
```

第三行: 我们把刚刚的delta结果加会到原始字符串中去，也就是:

```
G \ Namespace \ pack
+  0 3 000000000 3 0000
----------------------
   G _ Namespace _ pack
```

第四行：把结果写回内存
这样以来，我就可以用一条指令同时检测16个字符，效率会大大提升。我们来做个简单的测试，测试脚本在这里：

> replace_chr.c

下载下来以后，用-O3编译，在我的开发机上跑的结果是(结果会根据字符串中出现’\\’的位置和数量不同而有些许差异):

```
| Length |  Nomal |  SSE2  |  RAT |
-----------------------------------
|      4 |   6972 |   7183 |   3% |
|      8 |   3864 |   3696 |  -4% |
|     16 |   2184 |    924 | -58% |
|     32 |   1553 |   1009 | -35% |
|     64 |   1260 |    882 | -30% |
|    128 |   1344 |    923 | -31% |
|    256 |   1007 |    630 | -37% |
|    512 |    421 |    295 | -30% |
```

从结果上可以看到，在字符串长度小于16的时候，SSE2版本的速度略逊于普通版本，但是当字符串长度大于16以后，SSE2的版本的优势就非常明显了

ps: 如果使用-O0编译，则结果截然不同，测试结果如下

```
| Length |  Nomal |  SSE2  |  RAT |
-----------------------------------
|      4 |   8946 |  27888 |  212% |
|      8 |   4578 |  14070 |  207% |
|     16 |   3318 |   8274 |  149% |
|     32 |   2100 |   5376 |  156% |
|     64 |   1638 |   3696 |  126% |
|    128 |   1722 |   2730 |  59% |
|    256 |   1134 |   2100 |  85% |
|    512 |    672 |   1092 |  62% |
```

无论字符大小多大，普通版本都比SSE2版本快

参考：
[使用SSE2指令集加速字符替换](https://www.laruence.com/2020/03/09/5412.html)
