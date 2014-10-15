---
layout: post
title: AboutHibernateBatchOperation
tags: [LeetCode]
---

>	During a interview,I really think that interview is a joke,beacuse that guy ask me to recite code.
>One question is about how to store 3000 record into database without loop,I still do not have answer,at that
>time,I just know batch operation.Today,I meet batch operation again,so record it here.


Batch processing

A naive approach to inserting 100,000 rows in the database using Hibernate might look like this:


```java
	Session session = sessionFactory.openSession();
	Transaction tx = session.beginTransaction();
	for ( int i=0; i<100000; i++ ) {
		Customer customer = new Customer(.....);
		session.save(customer);
	}
	tx.commit();
	session.close();  
```

This would fall over with an OutOfMemoryException somewhere around the 50,000th row. That is because Hibernate caches all the newly inserted Customer instances in the session-level cache. In this chapter we will show you how to avoid this problem.

If you are undertaking batch processing you will need to enable the use of JDBC batching. This is absolutely essential if you want to achieve optimal performance. Set the JDBC batch size to a reasonable number (10-50, for example):


```java
	hibernate.jdbc.batch_size 20
```

Hibernate disables insert batching at the JDBC level transparently if you use an identity identifier generator.

You can also do this kind of work in a process where interaction with the second-level cache is completely disabled:

```java
	hibernate.cache.use_second_level_cache false
```

However, this is not absolutely necessary, since we can explicitly set the CacheMode to disable interaction with the second-level cache.


Batch inserts

When making new objects persistent flush() and then clear() the session regularly in order to control the size of the first-level cache.

```java
		Session session = sessionFactory.openSession();
	Transaction tx = session.beginTransaction();
	   
	for ( int i=0; i<100000; i++ ) {
		Customer customer = new Customer(.....);
		session.save(customer);
		if ( i % 20 == 0 ) { //20, same as the JDBC batch size
			//flush a batch of inserts and release memory:
			session.flush();
			session.clear();
		}
	}
	   
	tx.commit();
	session.close();
```
	
	乐此不疲～

2014-10-15 15:39:11









