---
layout: post
title: Refactoring:ImprovingTheDesignOfExistingCode(First)
tags: [Reading]
---

>Refactor method used:
>Extract Method, Move Method, Replace Conditional with Polymorphism, 
>Self Encapsulate Field, Replace Type Code with State/Strategy,etc.
>All this refacting methods make assigning responsibilities more reasonaly,
>maintaining more easily.

	1.Refactor means change the code structure without changing the function, and it will
not change the visible behavior of the existing software.

	2. There are two things you need to do when coding, add new features and refactor.when 
you add new features, you should not change the existing code,just add new features and make
sure that the features you added can pass all the tests.when you refactor the code, you should 
not add new features, just refine the structure,at this moment, you should not add any new test.
	
	3. Programs have two values, one is what it can do today, the other is what it can do tomorrow
	
	4. The values of indirect layers:
(1). Sharing the logic: For example, if a function is called in two places, or the function in a super
class is shared by two child class;


	5. Deprecated: When you want to change an interface, you need to retain the old interface,and let 
the new interface to implement the old one, and maintain both.

			The codes' bad smell
			
	1. Duplicated Code:
		Extract Method;
		Pull Up Method;
		Form Template Method to get a Template Method pattern;
		
	2. Long Method:
		Extract Method;
		Replace Temp with Query;
		Introduce Parameter Object;
		Preserve Whole Object;
		Decompose Conditional;
	3. Large Class:
		Extract Class;
		Extract Subclass;
	4. Long Parameter List:
		Replace Parameter with Method;
		Preserve Whole Object;
		Introduce Parameter Object;
	5. Switch statement:
		Use polimorphism instead of switch statement;
		
		
		
		
		
		
	Extract Method:
Change this code
 
```	
	void printOwing(double amount) {
		printBanner();
		
		//print details
		System.out.println("name: " + _name);
		System.out.println("amount: " + amount);
	}
```
to this

```	
	void printOwing(double amount) {
		printBanner();
		printDetails(amount);
	}
	
	void printDetails (double amount) {
		System.out.println("name: " + _name);
		System.out.println("amount: " + amount);
	}
```
Motivation:
	If there is a long method or a method which needs a comment to let people get across,it is better to extract
it to a method,there are several benefits like follows:
	1. The method will be in small granularity,and has more possibility to be reused;
	2. Make higher level method just like several lines of comments, make code easier to read;

Another thing need to be considered is whether the method has local variable:

1. method without local variable:

```	
```


```	
```

```	
```

```	
```

```	
```
```	
```
```	
```
```	
```
```	
```
```	
```
```	
```









```	
```

```	
```

	乐此不疲～

2014-10-17 10:00:11









