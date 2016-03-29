---
layout: post
title: PooledObjectFactory, BasePooledObjectFactory, PooledObject<T>, DefaultPooledObject<T>, ObjectPool<T>
tags: [commons-pool]
---

>PooledObjectFactory<T>, BasePooledObjectFactory<T>
>PooledObject<T>, DefaultPooledObject<T>
>ObjectPool<T>


 
###interface PooledObjectFactory<T>

####An interface defining life-cycle methods for instances to be served by an ObjectPool
####life-cycle methods, ObjectPool<T>, PooledObject<T>

```
//create a new instance
PooledObject<T> makeObject() throws Exception;

//is invoked on every instance that has been passivated before it is borrowed from the pool
void activateObject(PooledObject<T> p) throws Exception;

//is invoked on every instance when it is returned to the pool
void passivateObject(PooledObject<T> p) throws Exception;

void destroyObject(PooledObject<T> p) throws Exception;
boolean validateObject(PooledObject<T> p);
```

###public abstract class BasePooledObjectFactory<T> implements PooledObjectFactory<T>
###base framework implementation, but commons-dbcp does not use this class, PoolalbleConnectionFactory implements PooledObjectFactory<PoolableConnection> directly
```

public abstract T create() throws Exception;

public abstract PooledObject<T> wrap(T obj);

// for makeObject we need to call wrap() to wrap the object returned by create()
public PooledObject<T> makeObject() throws Exception {
    return wrap(create());
}

```

###interface ObjectPool<T>
```
usage:

Object obj = null;
try {
	obj = pool.borrowObject();
	try {
		//... use the object ...
	} catch (Exception e) {
		//invalidate the object
		pool.invalidateObject(obj);
		// do not return the object to the pool twice
		obj = null;
	} finally {
		// make sure the object is returned to the pool
		if (null != obj) {
			pool.returnObject(obj);
		}
	}
	
} catch(Exception e) {
	// failed to borrow an object
}

//1. instance created by PooledObjectFactory.makeObject();
//2. previously idle object, activated with PooledObjectFactory.activateObject(),
// and validate with PooledObjectFactory.validateObject().
borrowObject() throws Exception, NoSuchElementException, IllegalStateException

void returnObject(T obj) throws Exception;

```
###PooledObject<T>
#### Defines the wrapper that is used to track the addidtional information, such as 
#### state, for the pooled objects
```
    T getObject();
    long getCreateTime();
	long getActiveTimeMillis();
	long getIdleTimeMillis();
	long getLastBorrowTime();
	long getLastReturnTime();
	long getLastUsedTime(); // see TrackedUse

```

###DefaultPooledObject<T> implements PooledObject<T>
####fields used to track the object, 
```
	private final T object;
    private PooledObjectState state = PooledObjectState.IDLE; // @GuardedBy("this") to ensure transitions are valid
    private final long createTime = System.currentTimeMillis();
    private volatile long lastBorrowTime = createTime;
    private volatile long lastUseTime = createTime;
    private volatile long lastReturnTime = createTime;
    private volatile boolean logAbandoned = false;
    private volatile Exception borrowedBy = null;
    private volatile Exception usedBy = null;
    private volatile long borrowedCount = 0;
```




    
    Learning travel～

2016.03.24


