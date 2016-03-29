---
layout: post
title: TimerTask
tags: [java concurrency]
---

>Usage of TimerTask, for periodical execution of some task for example, eviction task in commons-pools(BaseGenericObjcetPool)


###TimerTask implements Runnable
```
	long nextExecutionTime;
	long period = 0;
	// action for timer task
	public abstract void run();
	//cancel the task
	 public boolean cancel() {
        synchronized(lock) {
            boolean result = (state == SCHEDULED);
            state = CANCELLED;
            return result;
        }
    }
    //
    public long scheduledExecutionTime() {
        synchronized(lock) {
            return (period < 0 ? nextExecutionTime + period
                               : nextExecutionTime - period);
        }
    }
```

###Timer, and inner class TaskQueue, TimerThread
```
1. Tasks maybe scheduled for one-time execution, or for repeated execution at regular intervals;
2. Each Timer object is a single background thread that is used to execute all the timer's tasks, so each task
	must be short;
3. java.util.concurrent.ScheduledThreadPoolExecutor
```

###TaskQueue
```
// just normal queue no need to sync, because the sequence dont care.
    private TimerTask[] queue = new TimerTask[128];

```

###TimerThread extend Thread
```
    boolean newTasksMayBeScheduled = true;
    private TaskQueue queue;
    //init TimerThread with a TaskQueue
    TimerThread(TaskQueue queue) {
        this.queue = queue;
    }

	public void run() {
        try {
            mainLoop();
        } finally {
            // Someone killed this Thread, behave as if Timer cancelled
            synchronized(queue) {
                newTasksMayBeScheduled = false;
                queue.clear();  // Eliminate obsolete references
            }
        }
    }

	// mainloop to judge whether to execute the TimerTask, by TimerTask's state and current time, executing time, etc.
    private void mainLoop() {
        while (true) {
            try {
                TimerTask task;
                boolean taskFired;
                synchronized(queue) {
                    // Wait for queue to become non-empty
                    while (queue.isEmpty() && newTasksMayBeScheduled)
                        queue.wait();
                    if (queue.isEmpty())
                        break; // Queue is empty and will forever remain; die

                    // Queue nonempty; look at first evt and do the right thing
                    long currentTime, executionTime;
                    task = queue.getMin();
                    synchronized(task.lock) {
                        if (task.state == TimerTask.CANCELLED) {
                            queue.removeMin();
                            continue;  // No action required, poll queue again
                        }
                        currentTime = System.currentTimeMillis();
                        executionTime = task.nextExecutionTime;
                        if (taskFired = (executionTime<=currentTime)) {
                            if (task.period == 0) { // Non-repeating, remove
                                queue.removeMin();
                                task.state = TimerTask.EXECUTED;
                            } else { // Repeating task, reschedule
                                queue.rescheduleMin(
                                  task.period<0 ? currentTime   - task.period
                                                : executionTime + task.period);
                            }
                        }
                    }
                    if (!taskFired) // Task hasn't yet fired; wait
                        queue.wait(executionTime - currentTime);
                }
                if (taskFired)  // Task fired; run it, holding no locks
                    task.run();
            } catch(InterruptedException e) {
            }
        }
    }

```

Learning travel～

2016-03-28