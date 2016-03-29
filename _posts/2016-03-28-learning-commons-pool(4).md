---
layout: post
title: BaseGenericObjectPool<T>
tags: [commons pool]
---

>Base class that provides common functionality for GenericObjectPool, GenericKeyedObjectPool, the primary reason 
>this class exists is reduce code duplication between the two pool implementations.

###Related Class: GenericKeydObjectPoolConfig, use this class to init all the config fields BaseGenericObjectPool contains.

###EvictionConfig
```
long idleEvictTime;
long idleSoftEvictTime;
int minIdel;
```

###EvictionPolicy<T>
```
	//test if an idle object in the pool should be evicted or not;
	boolean evict(EvictionConfig config, PooledObject<T> underTest, int idleCount);
```

###DefaultEvictionPolicy<T>
```
	@Override
	public boolean evict(EvictionConfig config, PooledObject<T> underTest, int idleCount) {
			if ((config.getIdelSoftEvictTime() < underTest.getIdleTimeMillis() && 
			config.getMinIdle() < idleCount) || 
			config.getIdleEvictTime() < underTest.getIdleTimeMillis()) {
			return true;
		};
		return false;
	}

```

###for evict feature in BaseGenericObjectPool<T>
```
1. final Object evictionLock = new Object();
2. private Evictor evictor = null; // @GuardedBy("evictionLock")
```

###Evictor
```
//all the direct method call is in BaseGenericObjectPool<T>

  // Inner classes

    /**
     * The idle object evictor {@link TimerTask}.
     *
     * @see GenericKeyedObjectPool#setTimeBetweenEvictionRunsMillis
     */
    class Evictor extends TimerTask {
        /**
         * Run pool maintenance.  Evict objects qualifying for eviction and then
         * ensure that the minimum number of idle instances are available.
         * Since the Timer that invokes Evictors is shared for all Pools but
         * pools may exist in different class loaders, the Evictor ensures that
         * any actions taken are under the class loader of the factory
         * associated with the pool.
         */
        @Override
        public void run() {
            ClassLoader savedClassLoader =
                    Thread.currentThread().getContextClassLoader();
            try {
                if (factoryClassLoader != null) {
                    // Set the class loader for the factory
                    ClassLoader cl = factoryClassLoader.get();
                    if (cl == null) {
                        // The pool has been dereferenced and the class loader
                        // GC'd. Cancel this timer so the pool can be GC'd as
                        // well.
                        cancel();
                        return;
                    }
                    Thread.currentThread().setContextClassLoader(cl);
                }

                // Evict from the pool
                try {
                    evict();
                } catch(Exception e) {
                    swallowException(e);
                } catch(OutOfMemoryError oome) {
                    // Log problem but give evictor thread a chance to continue
                    // in case error is recoverable
                    oome.printStackTrace(System.err);
                }
                // Re-create idle instances.
                try {
                    ensureMinIdle();
                } catch (Exception e) {
                    swallowException(e);
                }
            } finally {
                // Restore the previous CCL
                Thread.currentThread().setContextClassLoader(savedClassLoader);
            }
        }
    }
```

###EvictionTimer
```
    //a periodical long running thread, it is call by the constructor of BaseGenericObjectPool.
    final void startEvictor(long delay) {
        synchronized (evictionLock) {
            if (null != evictor) {
                EvictionTimer.cancel(evictor);
                evictor = null;
                evictionIterator = null;
            }
            if (delay > 0) {
                evictor = new Evictor();
                EvictionTimer.schedule(evictor, delay, delay);
            }
        }
    }

    //any way, it is start with the constructor meethod
    public GenericKeyedObjectPool(KeyedPooledObjectFactory<K,T> factory,
            GenericKeyedObjectPoolConfig config) {

        super(config, ONAME_BASE, config.getJmxNamePrefix());

        if (factory == null) {
            jmxUnregister(); // tidy up
            throw new IllegalArgumentException("factory may not be null");
        }
        this.factory = factory;
        this.fairness = config.getFairness();

        setConfig(config);

        startEvictor(getTimeBetweenEvictionRunsMillis());
    }
```

Learning travel～

2016-03-28