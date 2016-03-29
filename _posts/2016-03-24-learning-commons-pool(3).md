---
layout: post
title: ReadWriteLock, ReentrantReadWriteLock, Lock
tags: [java concurrency]
---

>I have met with a situation which creates a lock, a condition called notEmpty, another notFull,
>then when I am in lock.lock() block, I typed notEmpty.notify() by mistake (it should be notEmpty.signal()
>then java.lang.IllegalMonitorStateException is thrown, it cost me several minutes to correct it,
>then I decided to write these serial blogs to get through about the java concurrency (Lock(signal, await) (Synchronized block Object.notify(), await()) are two different mechanisms.)
>

###Lock Usage
```
	lock();
	try {
		// access the resource protected by this lock
	} finally {
		unlock();
	}

```

###tryLock Usage
```
	Lock lock = ...;
	if(lock.tryLock()) {
		try {
			//manipulate protected state
		} finally {
			lock.unlock();
		}
	} else {
		// perform alternative actions
	}
```

###boolean tryLock(long time, TimeUnit unit) throws InterruptedException;
```
// nothing to explain
```

###Condition newCondition();
```
	1. Returns a new Condition instance that is bound to this Lock instance;
	2. Before waiting on the condition the lock must be held by the current thread;
	3. Condition.await() will atomically release the lock;
	4. before waiting and re-acquire the lock before the wait returns;
```

    Learning travel～

2016-03-24
