---
layout: post
title: CleaningCode3
tags: [Reading]
---

>Make camp cleaner then it looks like when we came

Parameter Object

```
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

Parameter List

```
String.format("%s worked %.2f hours.", name, hours);
public String format(String format, Object... args);
```	

There is just one operation for each method,you can not let a method do something and answer something at the 
same time:

```
public boolean set(String attribute, String value);
if(set("username", "unclebob"))...
```
	
	乐此不疲～

2014-10-23 15:38:11









































































