---
layout: post
title: First post on commons-pool
tags: [commons-pool]
---

>In my own project, we need to connect to dif databases according to the env user chooses(the url can be anyone),
>so it seems mybatis or other database orm framework, not support these feature(because we need to config url in
>properties file, can not assign when the program is running), so I designed to maintain my own connection pool,
>and I finished a simple connection pool within several hours(the implementation will be introduced later), then
>I find it is a common issue to maintain a object pool if the object's creation will cost a lot of resources, 
>and for connection, commons-dbcp is a well designed framework, and it is based on commons-pool, commons-pool 
>is a framework for maintaining dif objects, so I designed to learn it from detail. 

 
###Implementation of my own connection pool

```
	Connections is an interface which contains:
	
	Connection getConnection(String url, String db);
	void releaseConnection(String url, String db, Connection connection)// maybe it should not be called release
	
	ConnectionsImpl which implements Connections contains:

	private final int POOL_SIZE = 3; // init connection number for 
    private final String URL_FORMAT = "jdbc:mysql://%s:3306/%s";

```
   
   
 - A 
 - B
 - C
 - D
 
```
123DFJLKAJLK 
```

```
```



    
    Learning travel～

2016.03.23


