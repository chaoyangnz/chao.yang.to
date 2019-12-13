---
title: A glimpse of JMS
toc: true
tags:
  - TODO
id: 789
categories:
  - JMS
date: "2015-04-01T14:59:19+00:00"
---

### what messaging provides?

*   **loosely decoupled**
A component sends a message to a destination, and the recipient can retrieve the message from the destination. However, the sender and the receiver do not have to be available at the same time in order to communicate. In fact, the sender does not need to know anything about the receiver; nor does the receiver need to know anything about the sender. The sender and the receiver need to know only which message format and which destination to use.

*   **asynchronous**
A JMS provider can deliver messages to a client as they arrive; a client does not have to request messages in order to receive them.

*   **reliable**
The JMS API can ensure that a message is delivered once and only once. Lower levels of reliability are available for applications that can afford to miss messages or to receive duplicate messages.

### JMS architecture

*   **JMS provider** - a messaging system implementing JMS specification, like WebSphere MQ, ActiveMQ etc.
*   **JMS clients** - programs or components written in Java which can produce or consume message
*   **messages** - the objects that communicate information between JMS clients.
*   **Administered objects** - preconfigured JMS objects created by an administrator for the use of clients. This objects can be looked up and referenced from JNDI namespace.
![jms\_parts](/media/jms_parts.png)

### Messaging Domains

#### Point-to-Point Messaging Domain

![ptp-messaging](/media/ptp-messaging.png)

* Each message has only one consumer.
* A sender and a receiver of a message have no timing dependencies. The receiver can fetch the message whether or not it was running when the client sent the message.
* The receiver acknowledges the successful processing of a message.

#### Publish/Subscribe Messaging Domain

![pub-sub-messaging](/media/pub-sub-messaging.png)

* Each message can have multiple consumers.
* Publishers and subscribers have a timing dependency. A client that subscribes to a topic can consume only messages published after the client has created a subscription, and the subscriber must continue to be active in order for it to consume messages.

### The JMS API Programming Model

![jms-programmingModel](/media/jms-programmingModel.gif)

#### JMS Administered Objects

Two parts of a JMS application, destinations and connection factories, are best maintained administratively rather than programmatically.

A **connection factory** is the object a client uses to create a connection to a provider.

In the PTP messaging domain, **destinations** are called queues. In the pub/sub messaging domain, **destinations** are called topics.

#### JMS Connections

You use a connection to create one or more sessions.

#### JMS Sessions

A **session** is a single-threaded context for producing and consuming messages. You use sessions to create the following:

*   Message producers
*   Message consumers
*   Messages
*   Queue browsers
*   Temporary queues and topics
Session class implements _Runnable_ interface.

#### JMS Message Producers

```java
MessageProducer producer = session.createProducer(dest);
MessageProducer producer = session.createProducer(queue);
MessageProducer producer = session.createProducer(topic);
producer.send(message);

MessageProducer anon_prod = session.createProducer(null);
anon_prod.send(dest, message);
```


#### JMS Message Consumers

```java
MessageConsumer consumer = session.createConsumer(dest);
MessageConsumer consumer = session.createConsumer(queue);
MessageConsumer consumer = session.createConsumer(topic);
 
connection.start();
Message m = consumer.receive();
connection.start();
Message m = consumer.receive(1000); // time out after a second
```


#### JMS Message Listeners

```java
Listener myListener = new Listener();
consumer.setMessageListener(myListener);
```


#### JMS Message Selectors

This is for message filtering.The syntax of the expression is based on a subset of the SQL92 conditional expression syntax.
`NewsType = ’Sports’ OR NewsType = ’Opinion’`
The message consumer then receives only messages whose headers and properties match the selector.

#### JMS Messages

JMS message includes 3 parts: header, properties, body, of which only header is required.

#### MS Queue Browsers

This browser can browse the messages in the queue and display the header values for each message.

### basic usage examples


```java
public class PointToPoint {

    private ConnectionFactory factory;
    private Connection connection;
    private Session session;
    private Destination destination;

    @Before
    public void setup() throws Exception {
        factory = new ActiveMQConnectionFactory(ActiveMQConnection.DEFAULT_BROKER_URL);
        connection = factory.createConnection();
        connection = factory.createConnection();
        connection.start();
        session = connection.createSession(false, Session.AUTO_ACKNOWLEDGE);
        destination = session.createQueue("myqueue");
    }

    @Test
    public void testSend() throws Exception {
        MessageProducer producer = session.createProducer(destination);
        TextMessage message = session.createTextMessage();
        message.setText("Hello ...This is a sample message..sending from FirstClient");
        producer.send(message);
        System.out.println("Sent: " + message.getText());
    }

    @Test
    public void testReceive() throws Exception {
        MessageConsumer consumer = session.createConsumer(destination);
        Message message = consumer.receive();

        if (message instanceof TextMessage) {
            TextMessage text = (TextMessage) message;
            System.out.println("Message is : " + text.getText());
        }
    }
}
```


### reliability mechanisms

#### acknowledge

#### transaction

#### message persistence

#### message expiry

#### message priority level

#### durable subscriber



