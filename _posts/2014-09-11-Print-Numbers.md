---
layout: post
title: print numbers recursively
tags: [LeetCode]
---

> Given only putchar (no sprintf, itoa, etc.) write a routine putlong that prints out an unsigned long in decimal.

分析：很简单的一道递归题，需要注意的两点在代码中已经标明，详见:[递归详解](http://blog.csdn.net/speedme/article/details/21654357)

```c
#include<stdio.h>
void putlong(unsigned long n) ;
main()
{
    putlong(1024) ;
}
void putlong(unsigned long n)
{
    long x = n/10 ;
    if(x!=0)            //递归必须要有结束条件
        putlong(x) ;
    putchar(n%10+'0') ; //后面加'0'表示数字的字符，从0开始往后
}
```


乐此不疲～

2014-09-11 16:19:31
