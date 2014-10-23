---
layout: post
title: RefactorHowToDealWithIfElse
tags: [Reading]
---

>We need to make code easy to read,not for efficiency but let it easy to maintain.

Code which smalls bad

```
String foundPerson(String[] people) {
	for(int i = 0; i < people.length; i++) {
		if(people[i].equals("Don")) {
			return "Don";
		}
		if(people[i].equals("John")) {
			return "John";
		}
		if(people[i].equals("Kent")) {
			return "Kent";
		}
	}
	return null;
}
```

change to:

```
	String foundPerson(String[] people) {
		List candidates = Array.asList(new String[] {"Don", "John", "Kent"});
		for(int i = 0; i < people.length; i++)
			if (candidates.contains(people[i]))
				return people[i];
		return "";		
	}
```
	乐此不疲～

2014-10-17 10:00:11






























