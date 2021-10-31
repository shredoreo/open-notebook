## AtomicBoolean

java.util.concurrent.atomic.AtomicBoolean

```java
//org.springframework.context.support.GenericApplicationContext
public class GenericApplicationContext extends AbstractApplicationContext implements BeanDefinitionRegistry {
...
private final AtomicBoolean refreshed = new AtomicBoolean();
  
  ...
    protected final void refreshBeanFactory() throws IllegalStateException {
    //spring容器在刷新前判断是不是刷新过 
		if (!this.refreshed.compareAndSet(false, true)) {
			throw new IllegalStateException(
					"GenericApplicationContext does not support multiple refresh attempts: just call 'refresh' once");
		}
		this.beanFactory.setSerializationId(getId());
	}
    
}
```

## java.lang.FunctionalInterface

```
@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface FunctionalInterface {}
```

## Assert

```java
Assert.notNull(dependencyType, "Dependency type must not be null");


public static void notNull(@Nullable Object object, String message) {
		if (object == null) {
			throw new IllegalArgumentException(message);
		}
	}
```





