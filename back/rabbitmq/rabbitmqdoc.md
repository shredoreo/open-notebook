# rabbitmq

```
"C:\Program Files\RabbitMQ Server\rabbitmq_server-3.8.0\sbin\rabbitmq-plugins" enable rabbitmq_management
```





## Hello world

 we create a channel, which is where most of the API for getting things done resides. Note we can use a try-with-resources statement because both `Connection` and `Channel` implement `java.io.Closeable`. This way we don't need to close them explicitly in our code. 

```java
public class Send {

    private final static String QUEUE_NAME = "hello";

    public static void main(String[] argv) throws Exception {
        ConnectionFactory factory = new ConnectionFactory();
        factory.setHost("localhost");
        //Try-with-resources是Java7中一个新的异常处理机制，它能够很容易地关闭在try-catch语句块中使用的资源。
        //实现了java中的java.lang.AutoCloseable接口。所有实现了这个接口的类都可以在try-with-resources结构中使用。
        try (Connection connection = factory.newConnection();
             Channel channel = connection.createChannel()) {
            channel.queueDeclare(QUEUE_NAME, false, false, false, null);
            String message = "Hello World!";
            channel.basicPublish("", QUEUE_NAME, null, message.getBytes("UTF-8"));
            System.out.println(" [x] Sent '" + message + "'");
        }
    }
}
//https://github.com/rabbitmq/rabbitmq-tutorials/blob/master/java/Send.java
```



 The message content is a byte array, so you can encode whatever you like there. 

 Declaring a queue is **idempotent** - it will only be created if it doesn't exist already. The message content is a byte array, so you can encode whatever you like there. 



### Receiving

That's it for our publisher. Our consumer listening for messages from RabbitMQ, so unlike the publisher which publishes a single message, we'll keep it running to listen for messages and print them out.

Why **don't we use** a try-with-resource statement to automatically close the channel and the connection? By doing so we would simply make the program move on, close everything, and exit! This would be awkward because we want the process to stay alive while the consumer is listening asynchronously for messages to arrive.

We're about to tell the server to deliver us the messages from the queue. Since it will push us messages asynchronously, we provide a **callback** in the form of an object that will **buffer the messages** until we're ready to use them. That is what a DeliverCallback subclass does.

```java
DeliverCallback deliverCallback = (consumerTag, delivery) -> {
    String message = new String(delivery.getBody(), "UTF-8");
    System.out.println(" [x] Received '" + message + "'");
};
channel.basicConsume(QUEUE_NAME, true, deliverCallback, consumerTag -> { });
```

## cli

```shell
#编译
javac -cp amqp-client-5.7.1.jar Send.java Recv.java
#运行
java -cp .;amqp-client-5.7.1.jar;slf4j-api-1.7.26.jar;slf4j-simple-1.7.26.jar Recv
java -cp .;amqp-client-5.7.1.jar;slf4j-api-1.7.26.jar;slf4j-simple-1.7.26.jar Send

#Hint
#To save typing, you can set an environment variable for the classpath e.g.
#linux：use `export` and seperator `:`
export CP=.:amqp-client-5.7.1.jar:slf4j-api-1.7.26.jar:slf4j-simple-1.7.26.jar
java -cp $CP Send

#or on Windows:
set CP=.;amqp-client-5.7.1.jar;slf4j-api-1.7.26.jar;slf4j-simple-1.7.26.jar
java -cp %CP% Send



```

![1571727871513](.\pic\rabbitmqdoc\srcstructure)

```shell
#my practise
D:\develop\rabbitmq\helloworldmq\src>
set CP=.;../lib/*
javac -cp %CP% ./com/shred/rabbit/helloworld/Recv.java
javac -cp %CP% ./com/shred/rabbit/helloworld/Send.java
java -cp %CP% com/shred/rabbit/helloworld/Send
# [x] Sent 'Hello world'
java -cp %CP% com/shred/rabbit/helloworld/Recv
# [*] Waiting for messages. To exit press CTRL+C
# [x] Received 'Hello world'
```





## Round-robin dispatching

 By default, RabbitMQ will send each message to the next consumer, in sequence. On average every consumer will get the same number of messages. This way of distributing messages is called round-robin( 轮询调度 ).  

## Message acknowledgment

Doing a task can take a few seconds. You may wonder what happens if one of the consumers starts a long task and dies with it only partly done. With our current code, once RabbitMQ delivers a message to the consumer it immediately marks it for deletion. In this case, if you kill a worker we will lose the message it was just processing. We'll also lose all the messages that were dispatched to this particular worker but were not yet handled.

But we don't want to lose any tasks. If a worker dies, we'd like the task to be delivered to another worker.

In order to make sure a message is never lost, RabbitMQ supports [message *acknowledgments*](https://www.rabbitmq.com/confirms.html). **An ack(nowledgement)** is sent back by the consumer to tell RabbitMQ that a particular message has been received, processed and that RabbitMQ is free to delete it.

If a consumer dies (its channel is closed, connection is closed, or TCP connection is lost) without sending an ack, RabbitMQ will understand that a message wasn't processed fully and will re-queue it. If there are other consumers online at the same time, it will then **quickly redeliver it to another consumer**. That way you can be sure that no message is lost, even if the workers occasionally die.

There **aren't any message timeouts**; RabbitMQ will redeliver the message when the consumer dies. It's fine even if processing a message takes a very, very long time.

 [Manual message acknowledgments](https://www.rabbitmq.com/confirms.html) are turned on by default. In previous examples we explicitly turned them off via the `autoAck=true` flag. 

## Message durability

We have learned how to make sure that even if the consumer dies, the task isn't lost. But our tasks will still be lost if RabbitMQ server stops.

When RabbitMQ quits or crashes it will forget the queues and messages unless you tell it not to. **Two things** are required to make sure that messages aren't lost: we need to mark both the queue and messages as **durable**.

1. queue

First, we need to make sure that RabbitMQ will never lose our queue. In order to do so, we need to declare it as *durable*:

```java
boolean durable = true;
channel.queueDeclare("hello", durable, false, false, null);
```

Although this command is correct by itself, it won't work in our present setup. That's because we've already defined a queue called `hello` which is not durable. RabbitMQ doesn't allow you to redefine an *existing queue* with different parameters and will return an error to any program that tries to do that. But there is a quick workaround - let's declare a queue with different name, for example task_queue:

```java
boolean durable = true;
channel.queueDeclare("task_queue", durable, false, false, null);
```

This `queueDeclare` change needs to be applied to both the producer and consumer code.

2. message

At this point we're sure that the task_queue queue won't be lost even if RabbitMQ restarts. Now we need to mark our messages as persistent - by setting `MessageProperties` (which implements `BasicProperties`) to the value `PERSISTENT_TEXT_PLAIN.`

```java
import com.rabbitmq.client.MessageProperties;

channel.basicPublish("", "task_queue",
            MessageProperties.PERSISTENT_TEXT_PLAIN,
            message.getBytes());
```

> #### Note on message persistence
>
> Marking messages as persistent doesn't fully guarantee that a message won't be lost. Although it tells RabbitMQ to save the message to disk, there is still a short time window when RabbitMQ has accepted a message and hasn't saved it yet. Also, RabbitMQ doesn't do `fsync(2)` for every message -- it may be just saved to cache and not really written to the disk. The persistence guarantees aren't strong, but it's more than enough for our simple task queue. If you need a stronger guarantee then you can use [publisher confirms](https://www.rabbitmq.com/confirms.html).

## Fair dispatch

You might have noticed that the dispatching still doesn't work exactly as we want. For example in a situation with two workers, when all odd messages are heavy and even messages are light, one worker will be constantly busy and the other one will do hardly any work. Well, RabbitMQ doesn't know anything about that and will still dispatch messages evenly.

This happens because RabbitMQ just dispatches a message when the message enters the queue. It doesn't look at the number of unacknowledged messages for a consumer. It just blindly dispatches every n-th message to the n-th consumer.

![img](https://www.rabbitmq.com/img/tutorials/prefetch-count.png)

In order to defeat that we can use the **`basicQos`** method with the **`prefetchCount = 1`** setting. *This tells RabbitMQ not to give more than one message to a worker at a time.* Or, in other words, don't dispatch a new message to a worker until it has processed and acknowledged the previous one. Instead, it will dispatch it to the next worker that is not still busy.

```java
int prefetchCount = 1;
channel.basicQos(prefetchCount);
```