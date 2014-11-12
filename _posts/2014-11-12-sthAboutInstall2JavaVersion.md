---
layout: post
title: sthAboutInstall2JavaVersion
tags: [Tips]
---

>Sometimes,we want to install two dif versions on our machine, but, that will not work if we just change the JAVA_HOME in path.


```
	This is because when we install jdk7, it will copy java.exe, javaw.exe and javaws.exe into C:\Windows\System32, and for this path
it has higher priority then JAVA_HOME, sometimes, so java -version, will still be java7, even if we set JAVA_HOME to java6. so the resolution
is to remove the three .exe in C:\Windows\System32, and then java_home will work.
```



	
	乐此不疲～

2014-11-12 13:41:11









































































