---
layout: page
title: About
description: 即非美玉, 也非瓦砾
keywords: Chunhui Cao, 曹春晖
comments: true
menu: 关于
permalink: /about/
---

我是monster，即非美玉， 也非瓦砾。

渴望找到属于自己的「目的地」。

能否到达「目的地」并不重要，而是有了「目的地」，便有了方向。

## 联系

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
{% if site.url contains 'mazhuang.org' %}
<li>
微信公众号：<br />
<img style="height:192px;width:192px;border:1px solid lightgrey;" src="{{ assets_base_url }}/assets/images/qrcode.jpg" alt="闷骚的程序员" />
</li>
{% endif %}
</ul>


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
