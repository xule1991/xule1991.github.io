---
layout: post
title: LearningActiveMq_Connection(activemq-client)_Hierarchy
tags: [activemq_learning]
---

>Connection Hierarchy 


 
###Hierarchy of Connection
   ![](http://xule1991.github.io/images/Connection_hierarchy.png)
   
###Functions Connection provided

- clientId
- connectionMetaData (info about provider and jms )
- exceptionListener (interface from jms)
- start()
- stop()
- close()
- createConeectionConsumer()
- createDurableConnectionConsumer()


```
Session createSession(boolean transacted, int acknowledgeMode)
        throws JMSException;

    String getClientID() throws JMSException;

    void setClientID(String clientID) throws JMSException;

    ConnectionMetaData getMetaData() throws JMSException;

    ExceptionListener getExceptionListener() throws JMSException;

    void setExceptionListener(ExceptionListener listener) throws JMSException;

    void start() throws JMSException;

    void stop() throws JMSException;

    void close() throws JMSException;

    ConnectionConsumer createConnectionConsumer(
        Destination destination,
        String messageSelector,
        ServerSessionPool sessionPool,
        int maxMessages)
        throws JMSException;

    ConnectionConsumer createDurableConnectionConsumer(
        Topic topic,
        String subscriptionName,
        String messageSelector,
        ServerSessionPool sessionPool,
        int maxMessages)
        throws JMSException;
```


###ExceptionListener
```
public interface ExceptionListener {
    void onException(JMSException exception);
}


```

###Implementations of ExceptionListener

####In ActiveMQManagedConnection (Observer/Listener Pattern)

```
 private final List<ConnectionEventListener> listeners = new CopyOnWriteArrayList<ConnectionEventListener>();
...
public void addConnectionEventListener(ConnectionEventListener listener) {
        listeners.add(listener);
    }
public void removeConnectionEventListener(ConnectionEventListener listener) {
        listeners.remove(listener);
    }
...
public void onException(JMSException e) {
        LOG.warn("Connection failed: " + e);
        LOG.debug("Cause: ", e);

        for (ManagedConnectionProxy proxy:proxyConnections) {
            proxy.onException(e);
        }
        // Let the container know that the error occurred.
        fireErrorOccurredEvent(e);
    }
    
    private void fireErrorOccurredEvent(Exception error) {
        ConnectionEvent event = new ConnectionEvent(ActiveMQManagedConnection.this, ConnectionEvent.CONNECTION_ERROR_OCCURRED, error);
        for(ConnectionEventListener l:listeners) {
            l.connectionErrorOccurred(event);
        }
    }
```

###set exceptioinlistener by creating anonymous object:
```
connection.setExceptionListener(new ExceptionListener() {
            @Override
            public void onException(JMSException exception) {
                exception.printStackTrace();
            }
        });
        
...
in activemq-broker JmsConnection:

newConnection.setExceptionListener(new ExceptionListener() {
            @Override
            public void onException(JMSException exception) {
                handleConnectionFailure(newConnection);
            }
        });
void handleConnectionFailure(Connection connection) {

        // Can happen if async exception listener kicks in at the same time.
        if (connection == null || !this.started.get()) {
            return;
        }

        LOG.info("JmsConnector handling loss of connection [{}]", connection.toString());

        // TODO - How do we handle the re-wiring of replyToBridges in this case.
        replyToBridges.clear();

        if (this.foreignConnection.compareAndSet(connection, null)) {

            // Stop the inbound bridges when the foreign connection is dropped since
            // the bridge has no consumer and needs to be restarted once a new connection
            // to the foreign side is made.
            for (DestinationBridge bridge : inboundBridges) {
                try {
                    bridge.stop();
                } catch(Exception e) {
                }
            }

            // We got here first and cleared the connection, now we queue a reconnect.
            this.connectionSerivce.execute(new Runnable() {

                @Override
                public void run() {
                    try {
                        doInitializeConnection(false);
                    } catch (Exception e) {
                        LOG.error("Failed to initialize foreign connection for the JMSConnector", e);
                    }
                }
            });

        } else if (this.localConnection.compareAndSet(connection, null)) {

            // Stop the outbound bridges when the local connection is dropped since
            // the bridge has no consumer and needs to be restarted once a new connection
            // to the local side is made.
            for (DestinationBridge bridge : outboundBridges) {
                try {
                    bridge.stop();
                } catch(Exception e) {
                }
            }

            // We got here first and cleared the connection, now we queue a reconnect.
            this.connectionSerivce.execute(new Runnable() {

                @Override
                public void run() {
                    try {
                        doInitializeConnection(true);
                    } catch (Exception e) {
                        LOG.error("Failed to initialize local connection for the JMSConnector", e);
                    }
                }
            });
        }
...

```

###threadsafe operation on AtomicReference<E>, compareAndSet

```
protected AtomicReference<Connection> foreignConnection = new AtomicReference<Connection>();

 if (this.foreignConnection.compareAndSet(connection, null)) {

            // Stop the inbound bridges when the foreign connection is dropped since
            // the bridge has no consumer and needs to be restarted once a new connection
            // to the foreign side is made.
...
```
######this code means if 1. the foreignConnection is connection and 2. set to null successfully, then do the code in if block.

###ThreadPoolExecutor
```
protected ThreadPoolExecutor connectionSerivce;

public boolean init() {
...
    connectionSerivce = createExecutor();
...
private ThreadPoolExecutor createExecutor() {
        ThreadPoolExecutor exec = new ThreadPoolExecutor(0, 2, 30, TimeUnit.SECONDS, new LinkedBlockingQueue<Runnable>(), factory);
        exec.allowCoreThreadTimeOut(true);
        return exec;
    }
...
 public ThreadPoolExecutor(int corePoolSize,
 // the number of threads to keep in the pool, even if they are idle, unless {@code allowCoreThreadTimeOut} is set
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             threadFactory, defaultHandler);
    }
...
 // We got here first and cleared the connection, now we queue a reconnect.
            this.connectionSerivce.execute(new Runnable() {

                @Override
                public void run() {
                    try {
                        doInitializeConnection(true);
                    } catch (Exception e) {
                        LOG.error("Failed to initialize local connection for the JMSConnector", e);
                    }
                }
            });
}
```


    
    Learning travel～

2015-12-13






































































