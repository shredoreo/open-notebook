### IllegalStateException

java.lang.IllegalStateException

```java
/**
 * Signals that a method has been invoked at an illegal or
 * inappropriate time.  In other words, the Java environment or
 * Java application is not in an appropriate state for the requested
 * operation.
 *
 * @author  Jonni Kanerva
 * @since   JDK1.1
 */
class IllegalStateException extends RuntimeException {
  }
```



### IllegalArgumentException

java.lang.IllegalArgumentException

```java
//org.springframework.boot.web.servlet.context.WebApplicationContextServletContextAwareProcessor#WebApplicationContextServletContextAwareProcessor
public WebApplicationContextServletContextAwareProcessor(ConfigurableWebApplicationContext webApplicationContext) {
		Assert.notNull(webApplicationContext, "WebApplicationContext must not be null");
		this.webApplicationContext = webApplicationContext;
	}

//org.springframework.util.Assert#notNull(java.lang.Object, java.lang.String)
public static void notNull(@Nullable Object object, String message) {
		if (object == null) {
			throw new IllegalArgumentException(message);
		}
	}
```

