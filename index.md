---
layout: page
title: 个人知识体系
tagline: 关注分布式系统、存储系统、C++、Rust、Go、音乐检索、AI落地、协程、操作系统内核、微处理器架构、
---

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


