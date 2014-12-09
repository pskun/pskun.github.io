---
layout: post
title: Python中的变量、赋值与拷贝问题（1）——问题提出、变量
description: ""
category: Python
avatarimg: "/img/head.png"
tags : [Python,变量,拷贝,浅拷贝,深拷贝,赋值]
duoshuo: true
---

## 问题的提出
----------
在网上看到一个帖子，说Python在某些情况下，i += x 不等于 i = i + x。

可以看下面一段代码：
{% highlight py %}
class foo:  
     bar = []
     def __init__(self,x):
         self.bar += [x]


class foo2:
     bar = []
     def __init__(self,x):
          self.bar = self.bar + [x]

f = foo(1)
g = foo(2)
print f.bar
print g.bar 

f.bar += [3]
print f.bar
print g.bar

f.bar = f.bar + [4]
print f.bar
print g.bar

f = foo2(1)
g = foo2(2)
print f.bar 
print g.bar 
{% endhighlight %}

输出结果：
{% highlight py %}
[1, 2]
[1, 2]
[1, 2, 3]
[1, 2, 3]
[1, 2, 3, 4]
[1, 2, 3]
[1]
[2]
{% endhighlight %}

是不是很诡异？

##Python的变量
----------
先抛开上面的问题暂时不谈，我们先来谈谈Python的变量。

变量是只不过保留的内存位置用来存储值。这意味着，当创建一个变量，那么它在内存中保留一些空间。可以这么认为，变量名称就代表着内存中的一个地址，变量实际上就是对象的引用。

Python在heap中分配的对象分成两类：可变对象和不可变对象。可变对象就是指对象的内容是可变的，不可变对象就是对象的内容是不可变的。不可变的对象元组，数值型和字符串，可变的对象有字典、列表。

那么，什么叫对象不可变呢？我们看下面的例子。
{% highlight py %}
>>> a="xyz"
>>> id(a)
36809472
>>> a="abc"
>>> id(a)
27249120
{% endhighlight %}

可以看到，同样是变量a，它的地址已经改变了。实际上Python是这么做的，在内存中申请一块新的内存，赋值为"abc"，把原来指向内存"xyz"的变量a指向内存"abc"，"xyz"的引用计数减一。当引用计数为0时，该内存地址被回收。

可变对象也是同理的，下面的例子可以很清晰地说明可变对象可以改变变量所指的内容。
{% highlight py %}
>>> l=[1]
>>> id(l)
35362016
>>> l.append(2)
>>> l
[1, 2]
>>> id(l)
35362016
{% endhighlight %}

不可变对象实际上也是内存地址的复用，这和C++的引用很像。
{% highlight py %}
>>> a=1
>>> b=1
>>> id(a)
25511144
>>> id(b)
25511144
{% endhighlight %}

如果内存中没有存整型值1的内存，则开辟之，然后引用a指向该内存。此时b=1这个语句就不用新申请一块内存，而是将引用b指向存整型值1的内存，引用计数加一。减少重复的值对内存空间的占用，缺点是如果内存中没用存在该值的内存块，那么必须重新开辟一块内存，把新地址与变量名绑定。而不是修改变量原来指向的内存块的值，这会给执行效率带来一定的降低。


