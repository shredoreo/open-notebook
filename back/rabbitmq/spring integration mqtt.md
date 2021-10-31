

## config

```java
MqttPahoClientFactory (MqttConnectOptions)

    
   1\ MqttPahoMessageHandler messageHandler =  new MqttPahoMessageHandler(clientId, mqttClientFactory());
    
    public MqttPahoMessageHandler(String clientId, MqttPahoClientFactory clientFactory) {
		super(null, clientId);
		this.clientFactory = clientFactory;
	}

2\ factory.setConnectionOptions(getMqttConnectOptions());
3\ MqttConnectOptions mqttConnectOptions=new MqttConnectOptions();


new DirectChannel();
```





### @MessagingGateway

使用gateway可以实现业务逻辑与`Spring Integration API`的解耦，只需要写一个接口，打注解即可。

- `@IntegrationComponentScan` 用于扫描`@MessagingGateway`注解的类，以便生成`GatewayProxyFactoryBean`

- `@MessagingGateway`

```java
@IntegrationComponentScan
/**
需要和@Configuration一起使用，用于扫描@MessagingGateway标注的类，用于生成GatewayProxyFactoryBean
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@Documented
@Import(IntegrationComponentScanRegistrar.class)
public @interface IntegrationComponentScan {
**/


@MessagingGateway(defaultRequestChannel = "mqttOutboundChannel")
//defaultRequestChannel：确认消息发往哪个channel
```



GatewayProxyFactoryBean

```java
public class GatewayProxyFactoryBean extends AbstractEndpoint
		implements TrackableComponent, FactoryBean<Object>, MethodInterceptor, BeanClassLoaderAware {

```





## inbound

 入站通道适配器由`MqttPahoMessageDrivenChannelAdapter`实现。 

```JAVA
public class MqttPahoMessageDrivenChannelAdapter extends AbstractMqttMessageDrivenChannelAdapter
		implements MqttCallback, ApplicationEventPublisherAware {
		
@ManagedResource
@IntegrationManagedResource
public abstract class AbstractMqttMessageDrivenChannelAdapter extends MessageProducerSupport {
    
    
public abstract class MessageProducerSupport extends AbstractEndpoint implements MessageProducer, TrackableComponent,
		SmartInitializingSingleton {
            
            
public interface MessageProducer {
		void setOutputChannel(MessageChannel outputChannel);
    	MessageChannel getOutputChannel();
}
```



## outbound

 出站通道适配器由`MqttPahoMessageHandler`封装在中`ConsumerEndpoint`。 

```JAVA
public class MqttPahoMessageHandler extends AbstractMqttMessageHandler
		implements MqttCallback, ApplicationEventPublisherAware {
    
    
public abstract class AbstractMqttMessageHandler extends AbstractMessageHandler implements Lifecycle {

    
@IntegrationManagedResource
public abstract class AbstractMessageHandler extends IntegrationObjectSupport
		implements MessageHandler, MessageHandlerMetrics, ConfigurableMetricsAware<AbstractMessageHandlerMetrics>,
		TrackableComponent, Orderable, CoreSubscriber<Message<?>> {
            

public abstract class IntegrationObjectSupport implements BeanNameAware, NamedComponent,ApplicationContextAware, BeanFactoryAware, InitializingBean, ExpressionCapable {
```



## amqp的message

- body
- messageProperties

```java
org.springframework.amqp.core.Message{
    public Message(byte[] body, MessageProperties messageProperties) { //NOSONAR
		this.body = body; //NOSONAR
		this.messageProperties = messageProperties;
	}
}

```

## spring通用message

在spring集成中使用的message

- playload
- headers

### Message

```java
//#org.springframework.messaging.Message
public interface Message<T> {

	/**
	 * Return the message payload.
	 */
	T getPayload();

	/**
	 * Return message headers for the message (never {@code null} but may be empty).
	 */
	MessageHeaders getHeaders();

}
    
```

实现

### GenericMessage

```java
public class GenericMessage<T> implements Message<T>, Serializable {

	private static final long serialVersionUID = 4268801052358035098L;

	private final T payload;

	private final MessageHeaders headers;
```



### MqttMessage

```java
/**
 * An MQTT message holds the application payload and options
 * specifying how the message is to be delivered
 * The message includes a "payload" (the body of the message)
 * represented as a byte[].
 */
public class MqttMessage {

	private boolean mutable = true;
	private byte[] payload;
	private int qos = 1;
	private boolean retained = false;
	private boolean dup = false;
	private int messageId;

```



## MessageConverter



```java
/**
 * A converter to turn the payload of a {@link Message} from serialized form to a typed
 * Object and vice versa. The {@link MessageHeaders#CONTENT_TYPE} message header may be
 * used to specify the media type of the message content.
 *
 * @author Mark Fisher
 * @author Rossen Stoyanchev
 * @since 4.0
 */
//org.springframework.messaging.converter.MessageConverter
public interface MessageConverter {

	/**
	 * Convert the payload of a {@link Message} from a serialized form to a typed Object
	 * of the specified target class. The {@link MessageHeaders#CONTENT_TYPE} header
	 * should indicate the MIME type to convert from.
	 * <p>If the converter does not support the specified media type or cannot perform
	 * the conversion, it should return {@code null}.
	 * @param message the input message
	 * @param targetClass the target class for the conversion
	 * @return the result of the conversion, or {@code null} if the converter cannot
	 * perform the conversion
	 */
	@Nullable
	Object fromMessage(Message<?> message, Class<?> targetClass);

	/**
	 * Create a {@link Message} whose payload is the result of converting the given
	 * payload Object to serialized form. The optional {@link MessageHeaders} parameter
	 * may contain a {@link MessageHeaders#CONTENT_TYPE} header to specify the target
	 * media type for the conversion and it may contain additional headers to be added
	 * to the message.
	 * <p>If the converter does not support the specified media type or cannot perform
	 * the conversion, it should return {@code null}.
	 * @param payload the Object to convert
	 * @param headers optional headers for the message (may be {@code null})
	 * @return the new message, or {@code null} if the converter does not support the
	 * Object type or the target media type
	 */
	@Nullable
	Message<?> toMessage(Object payload, @Nullable MessageHeaders headers);

}

```

### DefaultPahoMessageConverter

- 将message转化成MqttMessage，或者反之

-  `fromMessage(Message<?> message, Class<?> targetClass)`中，将message的payload放到MqttMessage中。 这里可以指定targetClass，但是实际上生成MqttMessage对象。

  > 是否可以通过重写的方式实现自定义的converter？

```java
//org.springframework.integration.mqtt.support.DefaultPahoMessageConverter
/**
 * Default implementation for mapping to/from Messages.
 */
public class DefaultPahoMessageConverter implements MqttMessageConverter, BeanFactoryAware {

	@Override
	public Message<?> toMessage(String topic, MqttMessage mqttMessage) {
		try {
			AbstractIntegrationMessageBuilder<Object> messageBuilder;
			if (this.bytesMessageMapper != null) {
				messageBuilder = (AbstractIntegrationMessageBuilder<Object>) getMessageBuilderFactory()
						.fromMessage(this.bytesMessageMapper.toMessage(mqttMessage.getPayload()));
			}
			else {
				messageBuilder = getMessageBuilderFactory()
					.withPayload(mqttBytesToPayload(mqttMessage));
			}
			messageBuilder
					.setHeader(MqttHeaders.RECEIVED_QOS, mqttMessage.getQos())
					.setHeader(MqttHeaders.DUPLICATE, mqttMessage.isDuplicate())
					.setHeader(MqttHeaders.RECEIVED_RETAINED, mqttMessage.isRetained());
			if (topic != null) {
				messageBuilder.setHeader(MqttHeaders.RECEIVED_TOPIC, topic);
			}
			return messageBuilder.build();
		}
		catch (Exception e) {
			throw new MessageConversionException("failed to convert object to Message", e);
		}
	}

	@Override
	public MqttMessage fromMessage(Message<?> message, Class<?> targetClass) {
		byte[] payloadBytes = messageToMqttBytes(message);
		MqttMessage mqttMessage = new MqttMessage(payloadBytes);
		Integer qos = this.qosProcessor.processMessage(message);
		mqttMessage.setQos(qos == null ? this.defaultQos : qos);
		Boolean retained = this.retainedProcessor.processMessage(message);
		mqttMessage.setRetained(retained == null ? this.defaultRetained : retained);
		return mqttMessage;
	}
}


-------
public interface MqttMessageConverter extends MessageConverter {

	/**
	 * Convert to a Message.
	 *
	 * @param topic The topic.
	 * @param mqttMessage The MQTT message.
	 * @return The Message.
	 */
	Message<?> toMessage(String topic, MqttMessage mqttMessage);

	static MessageProcessor<Integer> defaultQosProcessor() {
		return message -> message.getHeaders().get(MqttHeaders.QOS, Integer.class);
	}

	static MessageProcessor<Boolean> defaultRetainedProcessor() {
		return message -> message.getHeaders().get(MqttHeaders.RETAINED, Boolean.class);
	}

}
```

## 

