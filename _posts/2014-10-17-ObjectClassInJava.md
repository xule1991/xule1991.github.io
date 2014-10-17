---
layout: post
title: ObjectClassInJava
tags: [CoreJava]
---

> The class Object is very useful and a lot of advanced functions of java is based on this class
>so it is very helpful to know about the api of this class.

<h2><strong>The Class Object<strong></h2>

	The class Object is a superclass of all other classes.
All class and array types inherit the methods of class Object, which are
summarized as follows:



```
  The method "clone" is used to make a duplicate of an object.
```

```
  The method equals defines a notion of object equality, which is based on value,
not reference, comparison.
```

```
  The method finalize is run just before an object is destroyed
```

```
  The method getClass returns the Class object that represents the class of the
object.
A Class object exists for each reference type. It can be used, for example,
to discover the fully qualified name of a class, its members, its immediate
superclass, and any interfaces that it implements.
The type of a method invocation expression of getClass is Class<? extends
|T|> where T is the class or interface searched for getClass.
A class method that is declared synchronized synchronizes on the
monitor associated with the Class object of the class.
```

```
  The method hashCode is very useful, together with the method equals, in
hashtables such as java.util.Hashmap.
```

```
  The methods wait, notify, and notifyAll are used in concurrent programming
using threads
```

```
  The method toString returns a String representation of the object
```


	
	乐此不疲～

2014-10-17 10:00:11









