> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://www.jianshu.com/p/dca28ce0b3ec

22019.04.08 21:28:20 字数 908 阅读 483

#### SpringBoot 整合 RabbitMQ

SpringBoot 与 RabbitMQ 集成非常筒単，不需要做任何的额外设置只需要两步即可：  
step1：引入相关依赖：spring-boot-starter-amqp  
step2：対 application.properties 迸行配置

###### 生产端核心配置

![](asserts/RabbitMQ%20%E4%B8%8E%20SpringBoot%20%E6%95%B4%E5%90%88%E5%AE%9E%E6%88%98%20-%20%E7%AE%80%E4%B9%A6/14795543-382f63459f2c46bb.jpg)

###### 消费端核心配置

![](asserts/RabbitMQ%20%E4%B8%8E%20SpringBoot%20%E6%95%B4%E5%90%88%E5%AE%9E%E6%88%98%20-%20%E7%AE%80%E4%B9%A6/14795543-930b055f7d8ff275.jpg)

#### SpringBoot 整合 RabbitMQ 实战

1. 首先创建一个 Spring Boot 工程，这里使用 Spring Tool Suite 工具，选择导航菜单`File --> New --> Spring Starter Project`

![](asserts/RabbitMQ%20%E4%B8%8E%20SpringBoot%20%E6%95%B4%E5%90%88%E5%AE%9E%E6%88%98%20-%20%E7%AE%80%E4%B9%A6/14795543-9a744b7396e903f1.png)

2. 添加依赖

```
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-amqp</artifactId>
    </dependency>
```

然后复制一份工程，重命名为 rabbitmq-springboot-consumer，修改 pom 相关 artifactId，name 及 description

##### 生产端工程

添加生产端配置

```
# rabbitmq连接基本配置
spring.rabbitmq.addresses=192.168.0.113:5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.virtual-host=/
spring.rabbitmq.connection-timeout=15000

# 开启confirm机制
spring.rabbitmq.publisher-confirms=true
# 开启return模式
spring.rabbitmq.publisher-returns=true
# 配合return机制使用，表示接收路由不可达的消息
spring.rabbitmq.template.mandatory=true
```

创建配置类

```
@Configuration
@ComponentScan({"com.rxy.springboot.*"})
public class MainConfig {

}
```

###### 消息的 confirm 和 return 机制

*   **`publisher-confirms`，**实现一个监听器用于监听 Broker 端给我们返回的确认请求：`RabbitTemplate.ConfirmCallback`
*   **`publisher-returns`，**保证消息对 Broker 端是可达的，如果出现路由键不可达的情况，则使用监听器对不可达的消息进行后续的处理，保证消息的路由成功：`RabbitTemplate.ReturnCallback`
*   注意一点，在发送消息的时候对 template 进行配置**`mandatory=true`**保证监听有效
*   生产端还可以配置其他属性，比如发送重试，超时时间、次数、间隔等

创建生产端处理类

```java
import java.util.Map;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.rabbit.core.RabbitTemplate.ConfirmCallback;
import org.springframework.amqp.rabbit.core.RabbitTemplate.ReturnCallback;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.messaging.Message;
import org.springframework.messaging.MessageHeaders;
import org.springframework.messaging.support.MessageBuilder;
import org.springframework.stereotype.Component;

@Component
public class RabbitSender {

    //自动注入RabbitTemplate模板类
    @Autowired
    private RabbitTemplate rabbitTemplate;
    //确认机制
    final ConfirmCallback confirmCallback = new RabbitTemplate.ConfirmCallback() {
        /**
         * correlationData: 回调的相关数据，包含了消息ID
         * ack: ack结果，true代表ack，false代表nack
         * cause: 如果为nack，返回原因，否则为null
         */
        @Override
        public void confirm(CorrelationData correlationData, boolean ack, String cause) {
            System.err.println("correlationData: " + correlationData);
            System.err.println("ack: " + ack);
            if(!ack){
                //做一些补偿机制等
                System.err.println("异常处理....");
            }
        }
    };
    //返回机制
    final ReturnCallback returnCallback = new RabbitTemplate.ReturnCallback() {
        @Override
        public void returnedMessage(org.springframework.amqp.core.Message message, int replyCode, String replyText, 
                                    String exchange, String routingKey) {
            System.err.println("return exchange: " + exchange + ", routingKey: " 
                    + routingKey + ", replyCode: " + replyCode + ", replyText: " + replyText);
        }
    };
    
    //发送消息方法调用: 构建Message消息
    public void send(Object message, Map<String, Object> properties) throws Exception {
        MessageHeaders messageHeaders = new MessageHeaders(properties);
        //注意导包
        Message msg = MessageBuilder.createMessage(message, messageHeaders);
        rabbitTemplate.setConfirmCallback(confirmCallback);
        rabbitTemplate.setReturnCallback(returnCallback);
        //id + 时间戳 ，保证全局唯一 ，这个是实际消息的ID
        //在做补偿性机制的时候通过ID来获取到这条消息进行重发
        String id = "1234567890";
        CorrelationData correlationData = new CorrelationData(id);
        //exchange, routingKey, object, correlationData
        rabbitTemplate.convertAndSend("exchange-1", "springboot.abc", msg, correlationData);
    }
}
```

在管控台创建 topic 交换机 exchange-1 和队列 queue-1，并建立绑定关系为 springboot.#  
测试方法

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class RabbitmqSpringbootProducerApplicationTests {

    @Test
    public void contextLoads() {
    }

    @Autowired
    private RabbitSender rabbitSender;

    private static SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss.SSS");
    
    @Test
    public void testSender1() throws Exception {
         Map<String, Object> properties = new HashMap<>();
         properties.put("number", "12345");
         properties.put("send_time", simpleDateFormat.format(new Date()));
         rabbitSender.send("Hello RabbitMQ For Spring Boot!", properties);
    }
}
```

运行测试方法，打印如下内容

```
correlationData: CorrelationData [id=1234567890]
ack: true
```

将发送消息 convertAndSend 的 routingKey 修改为 spring.hello，再次运行测试方法，打印如下内容

```
return exchange: exchange-1, routingKey: spring.abc, replyCode: 312, replyText: NO_ROUTE
correlationData: CorrelationData [id=1234567890]
ack: true
```

##### 消费端工程

*   首先配置手工确认模式，用于 ACK 的手工处理，这样我们可以保证消息的可靠性送达，或者在消费端消费失败的时候可以做到重回队列 (不推荐)、根据业务记录日志等处理
*   可以设置消费端的监听个数和最大个数，用于控制消费端的并发情况

###### @RabbitMQListener 注解

*   消费端监听 @RabbitMQListener 注解，这个在实际工作中非常的好用。
*   @RabbitListener 是一个组合注解，里面可以注解配置：  
    @QueueBinding、@Queue、 @Exchange 直接通过这个组合注解一次性搞定消费端交换机、 队列、绑定、路由、并且配置监听功能等
*   由于类配置写在代码里非常不友好，所以强烈建议大家使用配置文件配置

![](http://upload-images.jianshu.io/upload_images/14795543-133a3e08e4c75ec4.png)

消费端配置

```
spring.rabbitmq.addresses=192.168.0.113:5672
spring.rabbitmq.username=guest
spring.rabbitmq.password=guest
spring.rabbitmq.virtual-host=/
spring.rabbitmq.connection-timeout=15000

# 设置签收模式：AUTO(自动签收)、MANUAL(手工签收)、NONE(不签收，没有任何操作)
spring.rabbitmq.listener.simple.acknowledge-mode=MANUAL
# 设置当前消费者数量(线程数)
spring.rabbitmq.listener.simple.concurrency=5
# 设置消费者最大并发数量
spring.rabbitmq.listener.simple.max-concurrency=10
```

消费端消息处理类

```java
import java.util.Map;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.amqp.support.AmqpHeaders;
import org.springframework.messaging.Message;
import org.springframework.stereotype.Component;

import com.rabbitmq.client.Channel;
import com.rxy.springboot.entity.Order;

@Component
public class RabbitReceiver {

    @RabbitListener(bindings = @QueueBinding(
            value = @Queue(value = "queue-1", 
            durable="true"),
            exchange = @Exchange(value = "exchange-1", 
            durable="true", 
            type= "topic", 
            ignoreDeclarationExceptions = "true"),
            key = "springboot.*"
            )
    )
    @RabbitHandler
    public void onMessage(Message message, Channel channel) throws Exception {
        System.err.println("--------------------------------------");
        System.err.println("消费端Payload: " + message.getPayload());
        Long deliveryTag = (Long)message.getHeaders().get(AmqpHeaders.DELIVERY_TAG);
        //手工ACK
        channel.basicAck(deliveryTag, false);
    }
}
```

前面生产端已经发送了一条消息到 queue-1 队列，可以再运行发送一条，然后运行消费端工程启动类 Application，打印如下

```
--------------------------------------
消费端Payload: Hello RabbitMQ For Spring Boot!
--------------------------------------
消费端Payload: Hello RabbitMQ For Spring Boot!
```

其实 @RabbitListener 会自动声明队列、交换机及绑定关系，可以在管控台删除对应的队列和交换机，然后重新运行进行测试

#### 使用配置方式

将队列、交换机、绑定关系使用配置方式，并且消息体内容使用 java 对象

1.  首先增加相关的配置

```
spring.rabbitmq.listener.order.queue.name=queue-2
spring.rabbitmq.listener.order.queue.durable=true
spring.rabbitmq.listener.order.exchange.name=exchange-2
spring.rabbitmq.listener.order.exchange.durable=true
spring.rabbitmq.listener.order.exchange.type=topic
spring.rabbitmq.listener.order.exchange.ignoreDeclarationExceptions=true
spring.rabbitmq.listener.order.key=springboot.*
```

另外两个工程都增加实体类 Order，注意要发送 java 对象，必须实现序列化接口

```java
public class Order implements Serializable {
    private String id;
    private String name;
}
```

2.  消费类增加 Order 对象消息的处理方法  
    `@Payload:` 接收消息的消息体对象  
    `@Headers:` 接收消息的属性  
    `AmqpHeaders:` 抽象类，里面包含了消息的常用属性 key

```java
@RabbitListener(bindings = @QueueBinding(
            value = @Queue(value = "${spring.rabbitmq.listener.order.queue.name}", 
            durable="${spring.rabbitmq.listener.order.queue.durable}"),
            exchange = @Exchange(value = "${spring.rabbitmq.listener.order.exchange.name}", 
            durable="${spring.rabbitmq.listener.order.exchange.durable}", 
            type= "${spring.rabbitmq.listener.order.exchange.type}", 
            ignoreDeclarationExceptions = "${spring.rabbitmq.listener.order.exchange.ignoreDeclarationExceptions}"),
            key = "${spring.rabbitmq.listener.order.key}"
            )
    )
    @RabbitHandler
    public void onOrderMessage(@Payload Order order, 
            Channel channel, 
            @Headers Map<String, Object> headers) throws Exception {
        System.err.println("--------------------------------------");
        System.err.println("消费端order: " + order.getId());
        Long deliveryTag = (Long)headers.get(AmqpHeaders.DELIVERY_TAG);
        //手工ACK
        channel.basicAck(deliveryTag, false);
    }
```

生产端发送方法，直接发送 java 对象

```java
//发送消息方法调用: 构建自定义对象消息
    public void sendOrder(Order order) throws Exception {
        rabbitTemplate.setConfirmCallback(confirmCallback);
        rabbitTemplate.setReturnCallback(returnCallback);
        //id + 时间戳 全局唯一 
        CorrelationData correlationData = new CorrelationData("0987654321");
        rabbitTemplate.convertAndSend("exchange-2", "springboot.def", order, correlationData);
    }
```

生产端测试方法

```java
@Test
    public void testSender2() throws Exception {
         Order order = new Order("001", "第一个订单");
         rabbitSender.sendOrder(order);
    }
```

###### 运行说明

先启动消费端，然后启动生产端

```bash
# 生产端打印
correlationData: CorrelationData [id=0987654321]
ack: true
# 消费端打印
--------------------------------------
消费端order: 001
```

