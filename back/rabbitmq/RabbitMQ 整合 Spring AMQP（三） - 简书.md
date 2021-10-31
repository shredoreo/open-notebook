> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://www.jianshu.com/p/b36f96a00d73

0.5532019.04.07 22:52:57 字数 1375 阅读 83

#### 消息监听适配器

*   `MessageListenerAdapter`，即消息监听适配器
*   前面我们介绍了通过实现`ChannelAwareMessageListener`接口并通过`onMessage`方法来接收消息。
*   除了这种方式我们也可以使用适配器对不同类型的消息进行适配处理。

###### 1. 定义消息处理类

委托类可以自己随意定义，但是这里的方法名和参数是固定的：`handleMessage`

```
public class MessageDelegate {

    public void handleMessage(byte[] messageBody) {
        System.err.println("默认方法, 消息内容:" + new String(messageBody));
    }
}


```

可以通过查看 MessageListenerAdapter 类的源码得知

![](asserts/simpread-RabbitMQ%20%E6%95%B4%E5%90%88%20Spring%20AMQP%EF%BC%88%E4%B8%89%EF%BC%89%20-%20%E7%AE%80%E4%B9%A6/14795543-3342a3976622ccf1.png)

###### 2. 设置消息监听器

这里将消息监听器设置为一个适配器对象，适配器需要一个委托对象。

```
    @Bean
    public SimpleMessageListenerContainer messageContainer(ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer(connectionFactory);
        
        
        
        
        
        MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
        container.setMessageListener(adapter);
        return container;
    }


```

###### 3. 运行说明

运行之前编写的测试方法进行消息发送

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

控制台打印了如下内容，说明通过适配器同样可以接收到消息

```
------添加额外的设置---------
默认方法, 消息内容:Hello RabbitMQ


```

##### 消息监听适配器相关设置

###### 1. 修改默认监听方法

设置方法：`adapter.setDefaultListenerMethod("consumeMessage");`  
在`MessageDelegate`类中添加方法

```
    public void consumeMessage(byte[] messageBody) {
        System.err.println("字节数组方法, 消息内容:" + new String(messageBody));
    }


```

再次运行测试方法，打印了如下内容，说明方法名修改是生效的。

```
------添加额外的设置---------
字节数组方法, 消息内容:Hello RabbitMQ


```

###### 2. 修改方法参数类型

修改为 String 类型：`adapter.setMessageConverter(new TextMessageConverter());`  
自定义转换器，实现`MessageConverter`接口

```
public class TextMessageConverter implements MessageConverter {

    
    @Override
    public Message toMessage(Object object, MessageProperties messageProperties) throws MessageConversionException {
        System.err.println("toMessage");
        return new Message(object.toString().getBytes(), messageProperties);
    }

    
    @Override
    public Object fromMessage(Message message) throws MessageConversionException {
        System.err.println("fromMessage");
        
        String contentType = message.getMessageProperties().getContentType();
        if(null != contentType && contentType.contains("text")) {
            return new String(message.getBody());
        }
        return message.getBody();
    }
}


```

修改委托类的监听方法参数

```
    public void consumeMessage(String messageBody) {
        System.err.println("字符串方法, 消息内容:" + messageBody);
    }


```

添加测试方法，指定消息的 contentType 属性

```
    @Test
    public void testSendMessage4Text() throws Exception {
        MessageProperties messageProperties = new MessageProperties();
        messageProperties.setContentType("text/plain");
        Message message = new Message("Test SpringAMQP Message".getBytes(), messageProperties);
        rabbitTemplate.send("topic001", "spring.abc", message);
        rabbitTemplate.send("topic002", "rabbit.abc", message);
    }


```

运行测试方法，打印了如下内容，说明方法参数类型修改是 OK 的。

```
fromMessage
字符串方法, 消息内容:Test SpringAMQP Message
fromMessage
字符串方法, 消息内容:Test SpringAMQP Message


```

###### 3. 队列名与方法名匹配

修改`MessageListenerAdapter`的配置，采用队列与方法名匹配方式，此时只有不匹配的队列消息才会走默认的监听方法。

```
    @Bean
    public SimpleMessageListenerContainer messageContainer(ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer(connectionFactory);
        
        
        MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
        adapter.setMessageConverter(new TextMessageConverter());
        Map<String, String> queueOrTagToMethodName = new HashMap<>();
        queueOrTagToMethodName.put("queue001", "method1");
        queueOrTagToMethodName.put("queue002", "method2");
        
        adapter.setQueueOrTagToMethodName(queueOrTagToMethodName);
        container.setMessageListener(adapter); 
        
        return container;
    }


```

`MessageDelegate`类添加消息监听方法

```
    public void method1(String messageBody) {
        System.err.println("method1 收到消息内容:" + new String(messageBody));
    }
    
    public void method2(String messageBody) {
        System.err.println("method2 收到消息内容:" + new String(messageBody));
    }


```

运行测试方法：testSendMessage4Text，控制台打印了如下内容

```
fromMessage
method1 收到消息内容:Test SpringAMQP Message
fromMessage
method2 收到消息内容:Test SpringAMQP Message


```

##### MessageListenerAdapter 总结

*   MessageListenerAdapter：即消息监听适配器
*   通过 messageListenerAdapter 的代码我们可以看出如下核心属性：  
    `defaultListenerMethod:` 默认监听方法名称: 用于设置监听方法名称  
    `Delegate:` 实际真实的委托对象，用于处理消息  
    `queueOrTagToMethodName:` 队列和方法名称绑定，即指定队列里的消息会被绑定的方法所接受处理

#### 消息转换器 - MessageConverter

*   我们在进行发送消息的时候，正常情况下消息体为二进制的数据方式进行传输，如果希望内部帮我们进行转换，或者指定自定义的转换器，就需要用到`MessageConverter`
*   自定义转换器通常是实现`MessageConverter`接口，重写下面两个方法：  
    `toMessage:` java 对象转换为 Message  
    `fromMessage:` Message 对象转换为 java 对象
*   3. 常用转换器  
    `Jackson2JsonMessageConverter:` Json 转换器，可以进行 java 对象的转换功能  
    `DefaultJackson2JavaTypeMapper:` 映射器，可以进行 java 对象的映射关系  
    自定义二进制转换器: 比如图片类型、PDF、 PPT、 流媒体

##### JSON 转换器的使用

###### 创建实体类

```
@Data
public class Order {
    private String id;
    private String name;
    private String content;
}
@Data
public class Packaged {
    private String packageId;
    private String packageName;
    private String description;
}


```

###### 配置转换器

在 RabbitMQConfig 配置类中配置 MessageListenerAdapter 对应的转换器

```
    @Bean
    public SimpleMessageListenerContainer messageContainer(ConnectionFactory connectionFactory) {
        SimpleMessageListenerContainer container = new SimpleMessageListenerContainer(connectionFactory);
        
        
        MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
        adapter.setDefaultListenerMethod("consumeMessage");
        
        Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
        adapter.setMessageConverter(jackson2JsonMessageConverter);
        container.setMessageListener(adapter);
        return container;
    }


```

###### 委托类中添加监听方法

在 MessageDelegate 中添加 JSON 格式的消息监听方法，对应的参数类型是 Map

```
public class MessageDelegate {
    public void consumeMessage(Map messageBody) {
        System.err.println("map方法, 消息内容:" + messageBody);
    }
}


```

###### 测试 json 转换器

编写测试方法，注意**设置消息的 contentType 属性**

```
    @Test
    public void testSendJsonMessage() throws Exception {
        Order order = new Order();
        order.setId("001");
        order.setName("消息订单");
        order.setContent("描述信息");
        
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(order);
        System.err.println("order 4 json: " + json);
        
        MessageProperties messageProperties = new MessageProperties();
        
        messageProperties.setContentType("application/json");
        Message message = new Message(json.getBytes(), messageProperties);
        rabbitTemplate.send("topic001", "spring.order", message);
    }


```

运行测试方法，控制台打印内容如下

```
order 4 json: {"id":"001","name":"消息订单","content":"描述信息"}
map方法, 消息内容:{id=001, name=消息订单, content=描述信息}


```

##### 对象映射器的使用

###### 配置转换器

在 RabbitMQConfig 配置类中配置 `MessageListenerAdapter` 对应的转换器，其实依然是使用 JSON 转换器，只不过需要另外对 JSON 转换器设置一个对象映射器，这样接收消息时就能使用对象接收了。  

- 方法：`jackson2JsonMessageConverter.setJavaTypeMapper(javaTypeMapper);`

```java
    MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
    adapter.setDefaultListenerMethod("consumeMessage");
    Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
   //将Mapper放入Converter中。设置json与java对象的映射器
    DefaultJackson2JavaTypeMapper javaTypeMapper = new DefaultJackson2JavaTypeMapper();
    jackson2JsonMessageConverter.setJavaTypeMapper(javaTypeMapper);
        
    adapter.setMessageConverter(jackson2JsonMessageConverter);
    container.setMessageListener(adapter);


```

###### 委托类中添加监听方法

在 MessageDelegate 中添加消息监听方法，对应的参数类型是 Java 对象

```java
    public void consumeMessage(Order order) {
        System.err.println("order对象, 消息内容, id: " + order.getId() + 
                ", name: " + order.getName() + 
                ", content: "+ order.getContent());
    }


```

###### 测试映射器

编写测试方法，注意设置消息的 java 对象类型: `messageProperties.getHeaders().put("__TypeId__", "xxx");`

- 设置java类型：key是固定的 \__TypeId__
- `DefaultJackson2JavaTypeMapper` 将识别\__TypeId__这个属性，并转换类型

```java
    @Test
    public void testSendJavaMessage() throws Exception {
        
        Order order = new Order("002","订单消息","订单描述信息");
        ObjectMapper mapper = new ObjectMapper();
        String json = mapper.writeValueAsString(order);
        System.err.println("order 4 json: " + json);
        
        MessageProperties messageProperties = new MessageProperties();
        
        messageProperties.setContentType("application/json");
        
        messageProperties.getHeaders().put("__TypeId__", "com.rxy.spring.entity.Order");
        Message message = new Message(json.getBytes(), messageProperties);
        
        rabbitTemplate.send("topic001", "spring.order", message);
    }


```

运行测试方法，控制台打印内容如下

```
order 4 json: {"id":"002","name":"订单消息","content":"订单描述信息"}
order对象, 消息内容, id: 002, name: 订单消息, content: 订单描述信息


```

##### Java 对象多映射转换

###### 配置转换器

- 方法: `javaTypeMapper.setIdClassMapping(idClassMapping);`  
  可以配置多个 Java 对象的映射关系，从而根据对象标识将 json 数据转换为不同的 Java 对象

```java
    MessageListenerAdapter adapter = new MessageListenerAdapter(new MessageDelegate());
    adapter.setDefaultListenerMethod("consumeMessage");
    Jackson2JsonMessageConverter jackson2JsonMessageConverter = new Jackson2JsonMessageConverter();
    DefaultJackson2JavaTypeMapper javaTypeMapper = new DefaultJackson2JavaTypeMapper();
//设置多种对象的mapper，将全类名映射为classId
    Map<String, Class<?>> idClassMapping = new HashMap<String, Class<?>>();
    idClassMapping.put("order", com.rxy.spring.entity.Order.class);
    idClassMapping.put("packaged", com.rxy.spring.entity.Packaged.class);
    javaTypeMapper.setIdClassMapping(idClassMapping);
    
    jackson2JsonMessageConverter.setJavaTypeMapper(javaTypeMapper);
    adapter.setMessageConverter(jackson2JsonMessageConverter);
    container.setMessageListener(adapter);


```

###### 委托类中添加监听方法

- 在 `MessageDelegate` 中添加消息监听方法，对应的参数类型是 Java 对象
- `DefaultJackson2JavaTypeMapper`  转换时，classID获取类名，再决定使用哪一种方法

```java
    public void consumeMessage(Order order) {
        System.err.println("order对象, 消息内容, id: " + order.getId() + 
                ", name: " + order.getName() + 
                ", content: "+ order.getContent());
    }
    
    public void consumeMessage(Packaged pack) {
        System.err.println("package对象, 消息内容, id: " + pack.getPackageId() + 
                ", name: " + pack.getPackageName() + 
                ", content: "+ pack.getDescription());
    }


```

###### 测试映射器

编写测试方法，注意设置消息的 java 对象类型，这里只需要设置 java 对象的标识即可

```java
    @Test
    public void testSendMappingMessage() throws Exception {
        
        ObjectMapper mapper = new ObjectMapper();
        
        Order order = new Order("003","订单消息","订单描述信息");
        String json1 = mapper.writeValueAsString(order);
        System.err.println("order 4 json: " + json1);
        
        MessageProperties messageProperties1 = new MessageProperties();
//这里注意一定要修改contentType为 application/json
        messageProperties1.setContentType("application/json");
//由于使用了javaTypeMapper.setIdClassMapping(idClassMapping);，这里只需要用类对应的id即可，不需要用全类名。
        messageProperties1.getHeaders().put("__TypeId__", "order");
        Message message1 = new Message(json1.getBytes(), messageProperties1);
        rabbitTemplate.send("topic001", "spring.order", message1);
        
        Packaged pack = new Packaged("003","包裹消息","包裹描述信息");
        String json2 = mapper.writeValueAsString(pack);
        System.err.println("pack 4 json: " + json2);

        MessageProperties messageProperties2 = new MessageProperties();
        
        messageProperties2.setContentType("application/json");
        messageProperties2.getHeaders().put("__TypeId__", "packaged");
        Message message2 = new Message(json2.getBytes(), messageProperties2);
        
        rabbitTemplate.send("topic001", "spring.pack", message2);
    }


```

运行测试方法，控制台打印内容如下，事实上只打印了前三行，因为 SpringBoot 运行完 Junit 测试后就会**自动停止**了，而不会等消息处理完成之后再关闭容器，此时可以再运行 Application 类就打印了第四行信息，也就是完成了第二条消息的处理。

```
order 4 json: {"id":"003","name":"订单消息","content":"订单描述信息"}
pack 4 json: {"packageId":"003","packageName":"包裹消息","description":"包裹描述信息"}
order对象, 消息内容, id: 003, name: 订单消息, content: 订单描述信息
package对象, 消息内容, id: 003, name: 包裹消息, content: 包裹描述信息


```

##### 全局转换器

上述介绍的转换器功能都相对单一，如果要处理更多场景下的不同类型消息，可以使用全局转换器。

###### 配置全局转换器

```
    
    ContentTypeDelegatingMessageConverter convert = new ContentTypeDelegatingMessageConverter();
    
    
    TextMessageConverter textConvert = new TextMessageConverter();
    convert.addDelegate("text", textConvert);
    convert.addDelegate("html/text", textConvert);
    convert.addDelegate("xml/text", textConvert);
    convert.addDelegate("text/plain", textConvert);
    
    Jackson2JsonMessageConverter jsonConvert = new Jackson2JsonMessageConverter();
    convert.addDelegate("json", jsonConvert);
    convert.addDelegate("application/json", jsonConvert);
    
    ImageMessageConverter imageConverter = new ImageMessageConverter();
    convert.addDelegate("image/jpg", imageConverter);
    convert.addDelegate("image", imageConverter);
    
    PDFMessageConverter pdfConverter = new PDFMessageConverter();
    convert.addDelegate("application/pdf", pdfConverter);
    
    adapter.setMessageConverter(convert);
    container.setMessageListener(adapter);


```

###### 定义相应的转换器

这里以图片转换器为例，最终转换的类型是 File

```
public class ImageMessageConverter implements MessageConverter {

    @Override
    public Message toMessage(Object object, MessageProperties messageProperties) throws MessageConversionException {
        throw new MessageConversionException(" convert error ! ");
    }

    @Override
    public Object fromMessage(Message message) throws MessageConversionException {
        System.err.println("-----------Image MessageConverter----------");
        
        Object _extName = message.getMessageProperties().getHeaders().get("extName");
        String extName = _extName == null ? "jpg" : _extName.toString();
        
        byte[] body = message.getBody();
        String fileName = UUID.randomUUID().toString();
        String path = "d:/photo/" + fileName + "." + extName;
        File f = new File(path);
        try {
            Files.copy(new ByteArrayInputStream(body), f.toPath());
        } catch (IOException e) {
            e.printStackTrace();
        }
        return f;
    }
}


```

###### 委托类中添加监听方法

```
    public void consumeMessage(File file) {
        System.err.println("文件对象方法, 消息内容:" + file.getName());
    }


```

###### 测试

编写测试方法，注意设置消息的 contentType

```
    @Test
    public void testSendExtConverterMessage() throws Exception {
            byte[] body = Files.readAllBytes(Paths.get("C:/Users/ruoxiyuan/Desktop", "picture.jpg"));
            MessageProperties messageProperties = new MessageProperties();
            messageProperties.setContentType("image/jpg");
            messageProperties.getHeaders().put("extName", "jpg");
            Message message = new Message(body, messageProperties);
            rabbitTemplate.send("", "image_queue", message);
    }


```

运行测试方法，控制台打印内容如下，其他几种类型转换器也可以自行验证

```
-----------Image MessageConverter----------
文件对象方法, 消息内容:35f00409-83ce-4bf0-881a-a3bc5d13f85e.jpg


```

[![](https://upload.jianshu.io/users/upload_avatars/14795543/d5d1ea27-82fa-4988-b575-30cf5a0c2ecc.jpg?imageMogr2/auto-orient/strip|imageView2/1/w/120/h/120/format/webp)](https://www.jianshu.com/u/29c2b026a2f1)

### 推荐阅读[更多精彩内容](https://www.jianshu.com/)

*   http://liuxing.info/2017/06/30/Spring%20AMQP%E4%B8%AD%E6%...
    
*   Swift1> Swift 和 OC 的区别 1.1> Swift 没有地址 / 指针的概念 1.2> 泛型 1.3> 类型严谨 对...
    
*   1. 简介 1.1 什么是 MyBatis ？ MyBatis 是支持定制化 SQL、存储过程以及高级映射的优秀的...
    
*   一、基础知识：1、JVM、JRE 和 JDK 的区别：JVM(Java Virtual Machine):java 虚拟机...
    
*   ORA-00001: 违反唯一约束条件 (.) 错误说明：当在唯一索引所对应的列上键入重复值时，会触发此异常。 O...