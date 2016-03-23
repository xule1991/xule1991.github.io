---
---
layout: post
title: Condition
tags: [java concurrency]
---

>Lock lock = new ReentrantLock();
>Condition notFull = lock.newCondition();
>Condition notEmpty = lock.newCondition();
>Condition(signal, await), Object(notify, wait) 


 
###BounderBuffer

###Fields in queue
```
final Lock lock = new ReentrantLock();//used to create try block for calling signal or await
final Condition notFull = lock.newCondition();// need to be called in lock.lock() block
final Condition notEmpty = lock.newCondition();// need to be called in lock.lock() block

// with no explain
final Object[] items = new Object[100];
int putptr /*write index*/, takeptr/*read index*/, count = 0/*num of object in array*/;

```

###put method of BounderBuffer
```

public void put(Object x) throws InterruptedException {
//lock the ReentrantLock, attention, lock.lock() will not get the lock's monitor object, so we can not call 
// the notify and wait methods in the try/finally block.
    lock.lock();
    try {
    	//if there are no object, thread need to be waiting on notFull Condition
        while (count == items.length)
            notFull.await();
        items[putptr] = x;

        if (++putptr == items.length) putptr = 0;
        ++count;
        //after put an object into the queue, we need to call signal() to wake up the thread blocked on notEmpty
        notEmpty.signal();
    } finally {
        lock.unlock();
    }
}
```

###take method of BounderBuffer
```
public Object take() throws InterruptedException {
    lock.lock();
    try {
        while (count == 0)
            notEmpty.await();
        Object x = items[takeptr];
        if (++takeptr == items.length) takeptr = 0;
        --count;
        notFull.signal();
        return x;
    } finally {
        lock.unlock();
    }
}
```

###Consumer thread
```
//Consumer class will contains a BounderBuffer instance which is shared with
//Producer class, when consumer thread started, it will call the consume method for 10 times,
//and wait for 10ms after each call, then thread ends.
static class Consummer implements Runnable {
    private BounderBuffer bounderBuffer;

    public Consummer(BounderBuffer bounderBuffer) {
        this.bounderBuffer = bounderBuffer;
    }

    public void run() {
        for (int i = 0; i < 10; i++) {
            this.consume();
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("consumming " + i);
        }
    }

    public void consume() {
        try {
            bounderBuffer.take();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

###Producer thread
```
static class Producer implements Runnable {
    private BounderBuffer bounderBuffer;

    public Producer(BounderBuffer bounderBuffer) {
        this.bounderBuffer = bounderBuffer;
    }

    public void run() {
        for (int i = 0; i < 10; i++) {
            this.produce();
            try {
                Thread.sleep(10);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("producing " + i);
        }
    }

    public void produce() {
        try {
            bounderBuffer.put(new Object());
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

###main method
```
public static void main(String[] args) {
    BounderBuffer bounderBuffer = new BounderBuffer();
    Thread producer = new Thread(new Producer(bounderBuffer));
    Thread consumer = new Thread(new Consummer(bounderBuffer));
    producer.start();
    consumer.start();
}
```

###Attention
####Object.notify()
```
for Object's notify and wait, let notify as an example:

Object.notify():

Wakes up a single thread that is waiting on this object's monitor. 
This method should only be called by a thread that is the owner
      of this object's monitor. A thread becomes the owner of the
      object's monitor in one of three ways:
    
      By executing a synchronized instance method of that object.
      By executing the body of a {@code synchronized} statement
      that synchronizes on the object.
      For objects of type {@code Class,} by executing a
      synchronized static method of that class.

      Only one thread at a time can own an object's monitor.

so for lock.lock() (ReentrantLock), we cannot call lock.notify()/wait(), or the condition 
of the lock .notify()/wait(), because the thread called the function does not contains the 
monitor object of its own.

or an exception of java.lang.IllegalMonitorStateException will be thrown

find this error by mistyping notEmpty.notify() in the lock.lock() block.

they are two dif sync strategy. 

```






    
    Learning travel～

2016-03-23

