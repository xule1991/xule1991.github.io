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

 
#Implementation of my own connection pool

### Interface explain:
```
	Connections is an interface which contains:
	
	Connection getConnection(String url, String db);
	void releaseConnection(String url, String db, Connection connection)// maybe it should not be called release
	
```
## Implementation explain:

### Fields explain:
```
ConnectionsImpl which implements Connections contains:

	private final int POOL_SIZE = 3; // init connection number for 
    private final String URL_FORMAT = "jdbc:mysql://%s:3306/%s"; // first ph is for url, second for db

	// key in map is for "url"_"db", list is the 3 connections for each url_db, 
    Map<String, List<Connection>> connections = new HashMap<String, List<Connection>>();
```

###public Connection getConnection(String url, String db)
```
//generate the complete url
String completeUrl = String.format(URL_FORMAT, url, db);

//init connection pool for the specified "url"_"db", just for the first time,
//we create 3 connections for each.

//just the first thread which call getConnection one this url, db will enter the sync block
//so we add this if condition to avoid sync every time when a thread call getConnection().

if (connections.get(url + "_" + db) == null) {
	//lock the whole map, and add three connections on this url_db    
    synchronized (connections) {
        if (connections.get(url + "_" + db) == null) {
            try {
                ArrayList<Connection> urlConnections = new ArrayList<Connection>();
                Class.forName("com.mysql.jdbc.Driver").newInstance();
                Connection con = null;
                for (int i = 0; i < POOL_SIZE; i++) {
                    con = DriverManager.getConnection(completeUrl, "username", "password");
                    urlConnections.add(con);
                }
                connections.put(url + "_" + db, urlConnections);
            } catch (Exception e) {
                throw new DBException("db error", e);
            }
        }
    }
}
```

###return a connection on the specified url_db
```
	//sync on the list of the url_db
    List<Connection> connectionsDB = connections.get(url + "_" + db);
	synchronized (connectionsDB) {
        if (connectionsDB.size() > 0) {
        	//get the first connection, remove it from the connections list then return the connection
            Connection connection = connectionsDB.get(0);
            connectionsDB.remove(connection);
            return connection;
        } else {
            try {
				// if the connection list of this url_db contains no connection currently, the getConnection thread 
				// need to wait for other thread which has connection to release
                connectionsDB.wait();
                // get again
                return getConnection(url, db);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
    return null;

```

###public void releaseConnection(String url, String db, Connection connection)
#####for the first time I forget to call this method to return the connection back to the connection list of url_db
#####then, for each app, we can just get 3 times of each url_db, then frontend will show status pending, because 
#####the backend thread on this ajax call is waiting for available connection of the url_db. 
```
 List<Connection> connectionsDB = connections.get(url + "_" + db);
    synchronized (connectionsDB) {
    	//add the connection back to the url_db list, then notify the thread waiting for the url_db connection
        connectionsDB.add(connection);
        connectionsDB.notify();
    }
```

###public void destroyConnections()
```
//never used it should be called when the tomcat is shutdowning, but I do not consider this because, the 
//app is small, we can just ignore this 3 connections on each url_db.
Iterator it = connections.entrySet().iterator();
while (it.hasNext()) {
    Map.Entry pair = (Map.Entry) it.next();
    Iterator itConnection = ((List)pair.getValue()).iterator();
    while (itConnection.hasNext()) {
        Connection con = (Connection) itConnection.next();
        try {
            con.close();
        } catch (SQLException e) {
            throw new DBException("close connection error", e);
        }
    }
}

```
    
    Learning travel～

2016.03.23


