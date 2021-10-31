> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://www.jianshu.com/p/1468b75ec969

0.352019.03.26 22:23:20 字数 749 阅读 146

#### 消息模板组件 - RabbitTemplate

*   我们在与 SpringAMQP 整合的时候进行发送消息的关键类
*   该类提供了丰富的发送消息方法，包括可靠性投递消息方法、回调监听消息接口 ConfirmCallback、返回值确认接口 ReturnCallback 等等。
*   同样我们需要进行注入到 Spring 容器中，然后直接使用
*   在与 Spring 整合时需要实例化，但是在与 SrpingBoot 整合时，在配置文件里添加配置即可

#### RabbitTemplate 的使用

声明 RabbitTemplate，在 RabbitMQConfig 类中声明

```
    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        return rabbitTemplate;
    }


```

###### 使用 RabbitTemplate 发送消息

`MessagePostProcessor`类通常用来设置消息的 Header 以及消息的属性，它允许在消息被转换器处理后对其进行进一步的修改，可以通过它对发送的消息进行统一的设置。

```
    @Test
    public void testSendMessage() throws Exception {
        
        MessageProperties messageProperties = new MessageProperties();
        
        messageProperties.getHeaders().put("desc", "信息描述..");
        messageProperties.getHeaders().put("type", "自定义消息类型..");
        Message message = new Message("Hello RabbitMQ".getBytes(), messageProperties);
        
        
        rabbitTemplate.convertAndSend("topic001", "spring.amqp", message, new MessagePostProcessor() {
            @Override
            public Message postProcessMessage(Message message) throws AmqpException {
                System.err.println("------添加额外的设置---------");
                message.getMessageProperties().getHeaders().put("desc", "额外修改的信息描述");
                message.getMessageProperties().getHeaders().put("attr", "额外新加的属性");
                return message;
            }
        });
    }


```

运行测试类，进入管控台的 Queues 菜单，点击`queue001`，然后下面有个`Get messages`，点击按钮就可以获取消息了。

![](http://upload-images.jianshu.io/upload_images/14795543-9c5d9598f0615027.png)

###### 使用 RabbitTemplate 发送消息 2

可以调用 send 方法发送消息，也可以直接发送字符串消息。

```
    @Test
    public void testSendMessage2() throws Exception {
        
        MessageProperties messageProperties = new MessageProperties();
        messageProperties.setContentType("text/plain");
        Message message = new Message("mq发送消息".getBytes(), messageProperties);
        
        
        rabbitTemplate.send("topic001", "spring.abc", message);
        
        
        rabbitTemplate.convertAndSend("topic001", "spring.amqp", "hello object message send!");
        rabbitTemplate.convertAndSend("topic002", "rabbit.abc", "hello object message send!");
    }


```

运行测试类，依然是进入管控台的 Queues 菜单，点击相应的队列，点击`Get messages`获取消息。

![](http://upload-images.jianshu.io/upload_images/14795543-de95b646ca33edc1.png)

#### 消息容器 - SimpleMessageListenerContainer

*   简单消息监听容器，这个类非常的强大，我们可以对他进行很多设置，对于消费者的配置项，这个类都可以满足
*   监听队列 (多个队列)、自动启动、自动声明功能
*   设置事务特性、事务管理器、事务属性、事务容量 (并发)、是否开启事务、回滚消息等
*   设置消费者数量、最小最大数量、批量消费
*   设置消息确认和自动确认模式、是否重回队列、异常捕获 handler 函数
*   设置消费者标签生成策略、是否独占模式、消费者属性等
*   设置具体的监听器、消息转换器等等

###### 特性说明

*   `SimpleMessageListenerContainer`可以进行**动态设置**， 比如在运行中的应用可以动态的修改其消费者数量的大小、接收消息的模式等
*   很多基于 RabbitMQ 的自制定化后端管控台在进行动态设置的时候，也是根据这一特去实现的。所以可以看出 SpringAMQP 非常的强大

##### SimpleMessageListenerContainer 使用

这里简单列举一些，还可以设置很多很多属性，包括后面要介绍的消息适配器和消息转换器。

```
    @Bean
    public SimpleMessageListenerContainer messageContainer(ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer(connectionFactory);
        
        container.setQueues(queue001(), queue002(), queue003(), queue_image(), queue_pdf());
        
        container.setConcurrentConsumers(1);
        
        
        container.setMaxConcurrentConsumers(5);
        
        container.setDefaultRequeueRejected(false);
        
        container.setAcknowledgeMode(AcknowledgeMode.AUTO);
        
        container.setConsumerTagStrategy(new ConsumerTagStrategy() {
            @Override
            public String createConsumerTag(String queue) {
                return queue + "_" + UUID.randomUUID().toString();
            }
        });
        
        
        container.setMessageListener(new ChannelAwareMessageListener() {
            @Override
            public void onMessage(Message message, Channel channel) throws Exception {
                String msg = new String(message.getBody());
                System.err.println("----------消费者: " + msg);
            }
        });
        return container;
    }


```

运行 Application 类，选择 Channel 菜单，点击表格中的 channel，可以看到队列对应的消费者情况，Consumer tag 就是根据我们指定的规则生成的。

![](http://upload-images.jianshu.io/upload_images/14795543-c188d6ffabda80c2.png)

关闭 Application，运行测试类进行消息发送

```
    @Test
    public void testSendMessage2() throws Exception {
        
        MessageProperties messageProperties = new MessageProperties();
        messageProperties.setContentType("text/plain");
        Message message = new Message("mq发送消息".getBytes(), messageProperties);
        
        
        rabbitTemplate.send("topic001", "spring.abc", message);
        
        
        rabbitTemplate.convertAndSend("topic001", "spring.amqp", "hello object message send!");
        rabbitTemplate.convertAndSend("topic002", "rabbit.abc", "hello object message send!");
    }


```

监听器监听到了消息并打印了如下内容

```
----------消费者: mq发送消息
----------消费者: hello object message send!
----------消费者: hello object message send!


```

[![](https://upload.jianshu.io/users/upload_avatars/14795543/d5d1ea27-82fa-4988-b575-30cf5a0c2ecc.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/120/h/120/format/webp)](https://www.jianshu.com/u/29c2b026a2f1)

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

*   http://liuxing.info/2017/06/30/Spring%20AMQP%E4%B8%AD%E6%...
  
*   Spring 整合 rabbitmq 实践（一）：基础 Spring 整合 rabbitmq 实践（三）：源码 3. 扩展实践 ...
  
    [![](https://upload-images.jianshu.io/upload_images/6989591-d804c9832c1d6e5d?imageMogr2/auto-orient/strip|imageView2/1/w/300/h/240/format/webp)](https://www.jianshu.com/p/9aec19a910b1)
*   2 工作队列（Work Queues） In the first tutorial we wrote progra...
  
*   RabbitMQ 即一个消息队列，主要是用来实现应用程序的异步和解耦，同时也能起到消息缓冲，消息分发的作用。 消息...
  
*   Swift1> Swift 和 OC 的区别 1.1> Swift 没有地址 / 指针的概念 1.2> 泛型 1.3> 类型严谨 对...