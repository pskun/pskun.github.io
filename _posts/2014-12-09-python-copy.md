---
layout: post
title: Python中的赋值与拷贝问题
description: ""
category: introduction
avatarimg: "/img/head.png"
tags : [Python,拷贝,浅拷贝,深拷贝,赋值]
duoshuo: true
---

## 问题的提出
----------
在网上看到一个帖子，说Python在某些情况下，i += x 不等于 i = i + x。
可以看下面一段代码：
{% highlight py %}
import sys
{% endhighlight %}