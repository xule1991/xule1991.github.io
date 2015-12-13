---
layout: post
title: LearningActiveMq_ActiveMQDestination_Hierarchy
tags: [activemq_learning]
---

>Hierarchy benifits 


 
###Hierarchy of ActiveMQDestion
   ![](http://xule1991.github.io/images/ActiveMQDestination.png)
   
### Base enum variables encapsulated in ActiveMQDestination(super class), and usage in child class:

```
    public abstract class ActiveMQDestination extends JNDIBaseStorable implements DataStructure, Destination, Externalizable, Comparable<Object> {

    public static final String PATH_SEPERATOR = ".";
    public static final char COMPOSITE_SEPERATOR = ',';

    public static final byte QUEUE_TYPE = 0x01;
    public static final byte TOPIC_TYPE = 0x02;
    public static final byte TEMP_MASK = 0x04;
    public static final byte TEMP_TOPIC_TYPE = TOPIC_TYPE | TEMP_MASK;
    //using | to implement containing both conditions in byte enum
    public static final byte TEMP_QUEUE_TYPE = QUEUE_TYPE | TEMP_MASK;

    public static final String QUEUE_QUALIFIED_PREFIX = "queue://";
    public static final String TOPIC_QUALIFIED_PREFIX = "topic://";
    public static final String TEMP_QUEUE_QUALIFED_PREFIX = "temp-queue://";
    public static final String TEMP_TOPIC_QUALIFED_PREFIX = "temp-topic://";
    public static final String IS_DLQ = "isDLQ";

    public static final String TEMP_DESTINATION_NAME_PREFIX = "ID:";

    private static final long serialVersionUID = -3885260014960795889L;

    protected String physicalName;

    protected transient ActiveMQDestination[] compositeDestinations;
    protected transient String[] destinationPaths;
    protected transient boolean isPattern;
    protected transient int hashValue;
    protected Map<String, String> options;
    }
...
 
```

####Static method for creating new ActiveMQDestination and its child classes based on two params

```
 public static ActiveMQDestination createDestination(String name, byte defaultType) {
        if (name.startsWith(QUEUE_QUALIFIED_PREFIX)) {
            return new ActiveMQQueue(name.substring(QUEUE_QUALIFIED_PREFIX.length()));
        } else if (name.startsWith(TOPIC_QUALIFIED_PREFIX)) {
            return new ActiveMQTopic(name.substring(TOPIC_QUALIFIED_PREFIX.length()));
        } else if (name.startsWith(TEMP_QUEUE_QUALIFED_PREFIX)) {
            return new ActiveMQTempQueue(name.substring(TEMP_QUEUE_QUALIFED_PREFIX.length()));
        } else if (name.startsWith(TEMP_TOPIC_QUALIFED_PREFIX)) {
            return new ActiveMQTempTopic(name.substring(TEMP_TOPIC_QUALIFED_PREFIX.length()));
        }

        switch (defaultType) {
            case QUEUE_TYPE:
                return new ActiveMQQueue(name);
            case TOPIC_TYPE:
                return new ActiveMQTopic(name);
            case TEMP_QUEUE_TYPE:
                return new ActiveMQTempQueue(name);
            case TEMP_TOPIC_TYPE:
                return new ActiveMQTempTopic(name);
            default:
                throw new IllegalArgumentException("Invalid default destination type: " + defaultType);
        }
    }
```

###getDestinationType() is override by its child class then using switch to distinguish which ActiveMQDestination it is

```
public abstract byte getDestinationType();
```

###This method is in ActiveMQDestination and inherited by all its child classes

```
public String getDestinationTypeAsString() {
        switch (getDestinationType()) {
            case QUEUE_TYPE:
                return "Queue";
            case TOPIC_TYPE:
                return "Topic";
            case TEMP_QUEUE_TYPE:
                return "TempQueue";
            case TEMP_TOPIC_TYPE:
                return "TempTopic";
            default:
                throw new IllegalArgumentException("Invalid destination type: " + getDestinationType());
        }
    }
```

###ActiveMQTempDestination contains ActiveMQConnection, which can be used to remove this destination
```
public abstract class ActiveMQTempDestination extends ActiveMQDestination {
...

    protected transient ActiveMQConnection connection;

...
public void delete() throws JMSException {
        if (connection != null) {
            connection.deleteTempDestination(this);
        }
    }
...

```
### ActiveMQTempTopic extends ActiveMQTempDestination implements TemporaryTopic, ActiveMQTempTopic contains delete() method, which is not implemented by ActiveMQTempTopic, but it is inherit directly from ActiveMQTempDestination 



    
    Learning travel～

2015-12-12






































































