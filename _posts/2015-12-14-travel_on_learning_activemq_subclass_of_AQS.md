---
---
layout: post
title: LearningActiveMq_LinkedBlockingQueue
tags: [activemq_learning]
---

>Connection Hierarchy 


 
 ###dif on FairSync and NonFairSync
 ######NonFairSync
 trying to acquire first, if failed, do the flow same as FairSync, call acquire(1)
 ```
/**
         * Performs lock.  Try immediate barge, backing up to normal
         * acquire on failure.
         */
        final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }
 ```

######FairSync
```
        final void lock() {
            acquire(1);
        }

```
###ReentrantLock
- Sync sync
- abstract static class Sync extends AbstractQueuedSynchronizer
- static final class NonfairSync extends Sync 
- static final class FairSync extends Sync 

```
    public void lock() {
        sync.lock();
    }
    
    in FairSync:
    
    final void lock() {
            acquire(1);
        }
        
    in NonFairSync:
    
    final void lock() {
            if (compareAndSetState(0, 1))
                setExclusiveOwnerThread(Thread.currentThread());
            else
                acquire(1);
        }
```

###Code from AQS
######aquire(int arg)

```
/**
     * Acquires in exclusive mode, ignoring interrupts.  Implemented
     * by invoking at least once {@link #tryAcquire},
     * returning on success.  Otherwise the thread is queued, possibly
     * repeatedly blocking and unblocking, invoking {@link
     * #tryAcquire} until success.  This method can be used
     * to implement method {@link Lock#lock}.
     *
     * @param arg the acquire argument.  This value is conveyed to
     *        {@link #tryAcquire} but is otherwise uninterpreted and
     *        can represent anything you like.
     */
    public final void acquire(int arg) {
        if (!tryAcquire(arg) &&
            acquireQueued(addWaiter(Node.EXCLUSIVE), arg))
            selfInterrupt();
    } 
```

######tryAcquire(arg) in FairSync
```
  /**
         * Fair version of tryAcquire.  Don't grant access unless
         * recursive call or no waiters or is first.
         */
        protected final boolean tryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (!hasQueuedPredecessors() &&
                    compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0)
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
```

######tryAcquire(arg) in NonFairSync
```
protected final boolean tryAcquire(int acquires) {
            return nonfairTryAcquire(acquires);
        }
 /**
         * Performs non-fair tryLock.  tryAcquire is
         * implemented in subclasses, but both need nonfair
         * try for trylock method.
         */
        final boolean nonfairTryAcquire(int acquires) {
            final Thread current = Thread.currentThread();
            int c = getState();
            if (c == 0) {
                if (compareAndSetState(0, acquires)) {
                    setExclusiveOwnerThread(current);
                    return true;
                }
            }
            else if (current == getExclusiveOwnerThread()) {
                int nextc = c + acquires;
                if (nextc < 0) // overflow
                    throw new Error("Maximum lock count exceeded");
                setState(nextc);
                return true;
            }
            return false;
        }
```




    
    Learning travel～

2015-12-14






































































