---
layout: post
title: 从微软面试题说开去
description: "详细讨论一个数的n次方的后k位和前k位数"
category: algorithm
avatarimg: "/img/head.png"
tags : [math,interview]
duoshuo: true
---

## 题目
----------
实验室有同学去微软ASG面试，回来后交流了一下面经，发现有题目比较有意思。

题目是这样的：求3的1000万次方的后3位。

其实，这个题目更加一般化的描述是，给定一个数m，求它的n次方的后k位数。

## 解题思路
----------
测试公式

$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$

### 最朴素的思路

{% highlight cpp %}

 #include <math.h>
 #include <stdio.h>
 int main()
 {
	 int t, n;
	 scanf("%d", &t);
	 while(t--)
	 {
		 scanf("%d", &n);
		 double f = n*log10((double)n);
		 double decimal = f - floor(f);
		 int ans = floor(pow(10, decimal));
		 printf("%d\n", ans);
	 }
 }

{% endhighlight %}
