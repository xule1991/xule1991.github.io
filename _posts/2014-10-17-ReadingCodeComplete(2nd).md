---
layout: post
title: ReadingCodeComplete(2nd)
tags: [Reading]
---

> Some understandings from reading reading Code complete(2nd)


"ADT" abstract data type:

```	
	We usually make ADT for common bottom layer data type to malipulate that data, do not operate bottom layer data directly,sth as follows:
	
	currentFont.setSize();
	currentFont.setBoldOn();
	currentFont.setBoldOff();
	currentFont.setItalicOn();
	currentFont.setItalicOff();
	currentFont.setTypeFace();
	
	etc.
	
	If you are not use OOP, you can not do this kind of operation on a object, you need to write the method directly, 
```

"Encapsulation"

```
	Abstraction makes you ignore the details of an implementation but Encapsulation makes the implementations invisible...
```

"Inheritance"

```
	If you just want to use an implementation of a class but not its interface,please use containing not inheritance.
```

"Routine, method, procedure, function"

```
A method

	1. should have single purpose;
	2. should prevent bad input data;
	3. should have java doc;
	
	one of the purpose of a routine is to avoid duplicated code,to make the code easier for developing, 
	debuging, doc and maintaining,but it is not all;
	
	We make routine to fewer complexity, make the code easier to read, avoid duplication, supporting subclassing
	we need to make sure that the methods are simple enough.(use method instead of inline code)
```	

Using input value incorrectly :

```	
	int sample(int inputVal) {
		inputVal = inputVal * CurrentMultiplier (inputVal);
		...
		return inputVal;
	}
```	

This piece of code is a bad use case,because the inputVal is changed during the code.

Better one:

```
	int sample(int inputVal) {
		int workingVal = inputVal;
		workingVal = inputVal * CurrentMultiplier (inputVal);
		...
		return workingVal;
	}
```

	乐此不疲～

2014-10-17 10:00:11








