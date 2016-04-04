---
layout: post
title: leetcode Easy题目：Rotate Array
description: ""
category: algorithm
avatarimg: "/img/head.png"
tags : [leetcode,array]
duoshuo: true
---

## 题目
----------
Rotate an array of n elements to the right by k steps.

For example, with n = 7 and k = 3, the array [1,2,3,4,5,6,7] is rotated to [5,6,7,1,2,3,4].

大意是给定一个数组，把倒数第k个数之后的交换到数组的最前面。

## 解题思路
----------
最自然的思路是申请k个数的空间保存后面k个数，然后把前面n-k个挪k个位置，然后再把后面k个拼到最前面。

但是这种思路必然要使用O(n)的空间。实在没有想到更好的办法，就先写了这个。

{% highlight cpp %}

class Solution {
public:
    void rotate(int nums[], int n, int k) {
        if (k > n)
            k = k % n;
        int* temp = (int*)malloc(sizeof(int) * k);
        memcpy(temp, nums+n-k, sizeof(int) * k);
        for(int i=n-k-1; i>=0; i--)
        {
            nums[(i+k)%n] = nums[i];
        }
        memcpy(nums, temp, sizeof(int) * k);
    }
}

{% endhighlight %}

这里要注意的是边界条件，k > n的情况。

## 牛逼的思路
----------
AC以后看了看discussion，瞬间给跪了。

{% highlight cpp %}

void rotate(int nums[], int n, int k) {
    k %= n; // if k > n then the final result is the same as k%n
    reverseArray(nums, n-k, n-1);
    reverseArray(nums, 0, n-k-1);
    reverseArray(nums, 0, n-1);
}

/**
 * rotate the array nums from start to end
**/
void reverseArray(int nums[],int start, int end){
    while(start < end){
        int temp = nums[start];
        nums[start++] = nums[end];
        nums[end--] = temp;
        // or you can simply code as "std::swap(nums[start++], nums[end--])" to replace above three lines
    }
}

{% endhighlight %}

其实就是先把前n-k倒置，把后k倒置，再把整个数组倒置，就完成了旋转。其中的原理用到了线性代数的东西。

(X^T·Y^T)^T = YX

哎，真是数学没好好学害死人啊。