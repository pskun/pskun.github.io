---
layout: post
title: ACM Steps题目：Left Most Digit
description: ""
category: algorithm
avatarimg: "/img/head.png"
tags : [array,acm]
duoshuo: true
---

## 题目
----------
Given a positive integer N, you should output the leftmost digit of N^N.

大意是给定一个正整数N，求N的N次方的最左边一个数。

## 解题思路
----------
一开始想用right most[那一题](http://acm.hdu.edu.cn/showproblem.php?pid=1061)的思路，找规律，但是发现没有什么好的规律可找，也可能是受上一题的思路影响太大了，一直没有跳出思维定式。

想了好几天，实在想不到了，就去网上翻博客去了。看完觉得，数学好真是牛逼啊。

首先，对于10的幂次10^c，可以转换成10^(a+b)，其中c=a+b，a>=1，b<1&&b>=0。那么10^(a+b)=10^a * 10^b。因为10^a必然是10的整数幂，而0<=b<1，所以1<=10^b<10，所以10^c的最左边一位由10^b决定。

好了，现在我们要找的就是b是多少。我们令N^N = 10^c，两边同时取10为底的对数，有log10(N^N) = log(10^c)，即Nlog10(N)=c，那么b就是Nlog10(N)的小数部分了。求出b以后，再求10^b即可得到leftmost digit。

下面是我的代码。

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
