---
layout: post
title: LargeDigitsAddBasedOnLinkedList
tags: [LeetCode]
---

> Write an implementation to compute large digit add based on Linked List
> 
> 

分析：该题是对字符串的修改，我们可以想到利用两个指向原字符串的指针p1,p2来对其进行修改，p2指针负责移动寻找空格，p1指针负责对原指针的修改。

```c
#include<stdio.h>
void removeSpace(char *str) ;
main()
{
    char str[] = "hell o !" ;
    removeSpace(str) ;
    printf("%s",str) ;
}

void removeSpace(char *str) {
  char *p1 = str, *p2 = str;
  do 
    while (*p2 == ' ')
      p2++;
  while (*p1++ = *p2++);
}
```


乐此不疲～

2014-09-11 15:11:11
