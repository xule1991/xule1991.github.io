---
layout: post
title: count the number of words in a string
tags: [LeetCode]
---

> Count the number of words in a string, where a word is defined to be a contiguous sequence of non-space characters.

> eg, “Hello, my name is John.” -> 5

分析：此题中我们可以利用一个flag来表示遍历字符串时的状态，当遍历字母是状态改为true，非字母时flag=false;这样我们就可以结合flag来数清单词的数量了。

```c
#include<stdio.h>
int countWords(char* str) ;
main()
{
    char str[] = "Hello,my name is, Lei." ;
    printf("%d",countWords(str)) ;
}
int countWords(char* str)
{
    bool flag = false ;
    int num = 0 ;
    while(*str)
    {
        if(*str<'z'&&*str>'A')
            flag = true ;
        else if((*str>'z'||*str<'A')&&flag)
        {
            num++ ;
            flag = false ;
        }
        str++ ;
    }
    return num ;
}
```


乐此不疲～

2014-09-11 15:50:50
