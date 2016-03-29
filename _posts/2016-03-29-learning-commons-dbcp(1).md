---
layout: post
title: TimerTask
tags: [commons-dbcp2]
---

>1. PoolingDriver
>2. PoolingDataSource
>3. PoolingConnection
>4. PoolablePreparedStatement
>5. PoolableConnectionMXBean
>6. PoolableConnectionFactory
>7. PoolableConnection
>8. PoolableCallableStatement

###Usage
```

	There are two primary ways to access the DBCP pool:
		1. java.sql.Driver; (org.apache.commons.dbcp2.PoolingDriver)
		2. javax.sql.DataSource; (org.apache.commons.dbcp2.PoolingDataSource)

	//create a PoolingDataSource
	GenericObjectPool connectionPool = new GenericObjectPool(null);
	ConnectionFactory connectionFactory = new DriverManagerConnectionFactory("jdbc:some:connect:string", "username", "password");
	PoolableConnectionFactory poolableConnectionFactory = new PoolableConnectionFactory(connectionFactory, connectionPool, null, null, false, true);
	PoolingDataSource dataSource = new PoolingDataSource(connectionPool);

	or 
	//create a PoolingDriver
	ConnectionFactory connectionFactory = new DriverManagerConnectionFactory("jdbc:some:connect:string", "username", "password");
	PoolableConnectionFactory poolableConnectionFactory = new PoolableConnectionFactory(connetionFactory, connectionPool, null, null, false, true);
	PoolingDriver driver = new PoolingDriver();
	driver.registerPool("example", connectionPool);

	org.apache.commons.dbcp2.PoolableConnectionFactory is used to implement the lifecycle methods of java.sql.Connection.

```

###Pooled objects
```
	1. Driver / DataSource;
	2. Connection;
	3. Statement, PreparedStatement / CallableStatement, PreparedCallableStatement;
	4. ResultSet;
```

###interface ConnectionFactory
```
	/**
	
	*/
	java.sql.Connection createConnection() throws SQLException;
```
Learning travel～

2016-03-29