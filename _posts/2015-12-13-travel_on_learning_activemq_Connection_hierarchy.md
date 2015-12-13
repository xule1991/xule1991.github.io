---
layout: post
title: LearningActiveMq_Connection_Hierarchy
tags: [activemq_learning]
---

>Connection Hierarchy 


 
###Hierarchy of Connection
   ![](http://xule1991.github.io/images/ActiveMQDestination.png)
   
###Functions Connection provided

- clientId
- connectionMetaData
- exceptionListener
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


    
    Learning travel～

2015-12-12






































































