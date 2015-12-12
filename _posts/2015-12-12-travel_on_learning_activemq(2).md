---
layout: post
title: LearningActiveMq (2)
tags: [activemq_learning]
---

>basic mechanism of activemq.


   ###Basic flow
   - Get JMS connection factory, based on the env we provide;
   - Create JMS connection using connection factory;
   - Create JMS session using connection;
   - Point destination;
   - create JMS producer on session;
   - create JMS consumer and add message listener;
   - operations on messages;
   - close all the resources;
   '

###Publish-Subscribe
   ![](http://dl2.iteye.com/upload/attachment/0086/5400/605c3d41-70af-3c3c-8f9d-0077a252964e.jpg)
#####Publisher
- Send mesgs to one or more topics by defining one or more topics

```java
    public Publisher() throws JMSException {  
        factory = new ActiveMQConnectionFactory(brokerURL);  
        connection = factory.createConnection();  
        try {  
        connection.start();  
        } catch (JMSException jmse) {  
            connection.close();  
            throw jmse;  
        }  
        session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);  
        producer = session.createProducer(null);  
    } 

...


    protected void setTopics(String[] stocks) throws JMSException {  
        destinations = new Destination[stocks.length];  
        for(int i = 0; i < stocks.length; i++) {  
            destinations[i] = session.createTopic("STOCKS." + stocks[i]);  
        }  
} 

...

    protected void sendMessage(String[] stocks) throws JMSException {  
        for(int i = 0; i < stocks.length; i++) {  
            Message message = createStockMessage(stocks[i], session);  
            System.out.println("Sending: " + ((ActiveMQMapMessage)message).getContentMap() + " on destination: " + destinations[i]);  
            producer.send(destinations[i], message);  
        }  
    }  
  
    protected Message createStockMessage(String stock, Session session) throws JMSException {  
        MapMessage message = session.createMapMessage();  
        message.setString("stock", stock);  
        message.setDouble("price", 1.00);  
        message.setDouble("offer", 0.01);  
        message.setBoolean("up", true);  
              
        return message;  
    }  


```

#####Consumer
```java
public static void main(String[] args) throws JMSException {  
    Consumer consumer = new Consumer();  
    for (String stock : args) {  
    Destination destination = consumer.getSession().createTopic("STOCKS." + stock);  
    MessageConsumer messageConsumer = consumer.getSession().createConsumer(destination);  
    messageConsumer.setMessageListener(new Listener());  
    }  
}  
      
public Session getSession() {  
    return session;  
}  

...

public class Listener implements MessageListener {  
  
    public void onMessage(Message message) {  
        try {  
            MapMessage map = (MapMessage)message;  
            String stock = map.getString("stock");  
            double price = map.getDouble("price");  
            double offer = map.getDouble("offer");  
            boolean up = map.getBoolean("up");  
            DecimalFormat df = new DecimalFormat( "#,###,###,##0.00" );  
            System.out.println(stock + "\t" + df.format(price) + "\t" + df.format(offer) + "\t" + (up?"up":"down"));  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
} 




```

###P2P
   ![](http://dl2.iteye.com/upload/attachment/0086/5406/847f46bb-3d45-3e6f-b663-05669360c5b1.jpg)
   
####Sender
```java

public Publisher() throws JMSException {  
    factory = new ActiveMQConnectionFactory(brokerURL);  
    connection = factory.createConnection();  
    connection.start();  
    session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);  
    producer = session.createProducer(null);  
}  

...

public void sendMessage() throws JMSException {  
    for(int i = 0; i < jobs.length; i++)  
    {  
        String job = jobs[i];  
        Destination destination = session.createQueue("JOBS." + job);  
        Message message = session.createObjectMessage(i);  
        System.out.println("Sending: id: " + ((ObjectMessage)message).getObject() + " on queue: " + destination);  
        producer.send(destination, message);  
    }  
}  

...

public static void main(String[] args) throws JMSException {  
    Publisher publisher = new Publisher();  
    for(int i = 0; i < 10; i++) {  
        publisher.sendMessage();  
        System.out.println("Published " + i + " job messages");  
    try {  
            Thread.sleep(1000);  
        } catch (InterruptedException x) {  
        e.printStackTrace();  
        }  
    }  
    publisher.close();  
}  


```
####Receiver

```java
public Consumer() throws JMSException {  
        factory = new ActiveMQConnectionFactory(brokerURL);  
        connection = factory.createConnection();  
        connection.start();  
        session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);  
    }  
    ...
    public static void main(String[] args) throws JMSException {  
        Consumer consumer = new Consumer();  
        for (String job : consumer.jobs) {  
            Destination destination = consumer.getSession().createQueue("JOBS." + job);  
            MessageConsumer messageConsumer = consumer.getSession().createConsumer(destination);  
            messageConsumer.setMessageListener(new Listener(job));  
        }  
    }  
      
    public Session getSession() {  
        return session;  
    }
    
    ...
    
    import javax.jms.Message;  
import javax.jms.MessageListener;  
import javax.jms.ObjectMessage;  
  
public class Listener implements MessageListener {  
  
    private String job;  
      
    public Listener(String job) {  
        this.job = job;  
    }  
  
    public void onMessage(Message message) {  
        try {  
            //do something here  
            System.out.println(job + " id:" + ((ObjectMessage)message).getObject());  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
    }  
  
}  

```
###Request and Response
   ![](http://dl2.iteye.com/upload/attachment/0086/5402/b031376e-2af7-3502-b235-8c4db1ad890f.jpg)
```
    
 
```



	
	乐此不疲～

2015-12-12






































































