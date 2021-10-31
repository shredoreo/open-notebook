> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/weixin_50518271/article/details/108554441)

建议先阅读上一篇文章 [Dubbo 之 @SPI](https://blog.csdn.net/weixin_50518271/article/details/108475521)，效果更佳。

> **关键词：**@Adaptive

百度一下 adaptive，翻译是这样的：

**![](https://img-blog.csdnimg.cn/20200912202430383.png)**

个人觉得结合 Dubbo 中的 **@Adaptive** 的功能，合适的翻译是能适应的。而 @Adaptive 真的做到了根据参数能适应地返回不同的结果对象。

**█ 如何使用**
==========

@Adaptive，可以在两个地方使用：接口方法上或者接口的实现类上。

①修改 AnimalService 接口，添加两个方法。在方法上标注 @Adaptive 注解。

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
import com.alibaba.dubbo.common.extension.Adaptive;
import com.alibaba.dubbo.common.extension.SPI;
 
@SPI
public interface AnimalService {
 
    @Adaptive({"firstName"})
    void show(URL url);
 
    @Adaptive({"lastName"})
    void run(Animal animal);
 
    void say();
 
}
```

 第二个方法用到了一个 Animal 类，Animal 的代码如下：

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
 
public class Animal {
 
    private URL url;
 
    public void setUrl(URL url) {
        this.url = url;
    }
 
    public URL getUrl() {
        return url;
    }
 
}
```

②修改 DogService 类，并且新增一个接口实现类 CatService。在配置文件中添加 CatService 的配置。

```
package study.rui.dubbo.impl;
 
import com.alibaba.dubbo.common.URL;
import study.rui.dubbo.Animal;
import study.rui.dubbo.AnimalService;
 
public class DogService implements AnimalService {
 
    @Override
    public void say() {
        System.out.println("dog say woo woo");
    }
 
    @Override
    public void show(URL url) {
        System.out.println("dog" + url.toString());
    }
 
    @Override
    public void run(Animal animal) {
        System.out.println("dog" + animal.getUrl().toString());
    }
}
```

```
package study.rui.dubbo.impl;
 
import com.alibaba.dubbo.common.URL;
import com.alibaba.dubbo.common.extension.Adaptive;
import study.rui.dubbo.Animal;
import study.rui.dubbo.AnimalService;
 
@Adaptive
public class CatService implements AnimalService {
 
    @Override
    public void say() {
        System.out.println("cat say miao miao");
    }
 
    @Override
    public void show(URL url) {
        System.out.println("cat" + url.toString());
    }
 
    @Override
    public void run(Animal animal) {
        System.out.println("cat" + animal.getUrl().toString());
    }
}
```

```
dog=study.rui.dubbo.impl.DogService
cat=study.rui.dubbo.impl.CatService
```

③运行代码。

```
import com.alibaba.dubbo.common.extension.ExtensionLoader;
import study.rui.dubbo.AnimalService;
 
public class AdaptiveTest {
 
    public static void main(String[] args) {
 
        ExtensionLoader<AnimalService> loader = ExtensionLoader.getExtensionLoader(AnimalService.class);
        // 获取到CatService实例
        AnimalService animalService = loader.getAdaptiveExtension();
        // 返回study.rui.dubbo.impl.CatService
        System.out.println(animalService.getClass().getName());
 
    }
 
}
```

对于③中的运行结果，为什么 **getAdaptiveExtension()** 返回的是 CatService 实例呢。因为在 CatService 上标注了 @Adaptive 注解。

```
// 获取能适应的扩展类Class
private Class<?> getAdaptiveExtensionClass() {
    this.getExtensionClasses();
    // cachedAdaptiveClass不为null，则直接返回cachedAdaptiveClass，后面创建cachedAdaptiveClass类实例
    // cachedAdaptiveClass为空，则通过createAdaptiveExtensionClass方法动态生成一个实例。
    return this.cachedAdaptiveClass != null ? this.cachedAdaptiveClass : (this.cachedAdaptiveClass = this.createAdaptiveExtensionClass());
}
```

**cachedAdaptiveClass** 什么时候是 null，什么时候又不是 null 呢。在 **ExtensionLoader** 的 **loadClass** 方法中：

```
private void loadClass(Map<String, Class<?>> extensionClasses, java.net.URL resourceURL, Class<?> clazz, String name) throws NoSuchMethodException {
    ......
        // 如果clazz上标注了@Adaptive注解，clazz是接口的实现类的Class对象。
        // 例子中就是DogService.class和CatService.class
        if (clazz.isAnnotationPresent(Adaptive.class)) {
            // CatService进入
            if (this.cachedAdaptiveClass == null) {
                this.cachedAdaptiveClass = clazz;
            } 
            // 这里规定了接口的实现类中，有且仅有一个实现类能够标注@Adaptive
            else if (!this.cachedAdaptiveClass.equals(clazz)) {
                throw new IllegalStateException("More than 1 adaptive class found: " + this.cachedAdaptiveClass.getClass().getName() + ", " + clazz.getClass().getName());
            }
        } 
        ......
}
```

④去掉 CatService 上的 @Adaptive。

⑤运行代码。

```
public class AdaptiveTest {
 
    public static void main(String[] args) {
 
        ExtensionLoader<AnimalService> loader = ExtensionLoader.getExtensionLoader(AnimalService.class);
        AnimalService animalService = loader.getAdaptiveExtension();
 
        URL url = new URL("http", "localhost", 80);
        // 这里要特别注意addParameter方法
        // 如果url的参数中有了firstName，并且firstName的值就是cat，则会返回当前url实例
        // 否则会重新new 一个URL对象的
        URL newUrl = url.addParameter("firstName", "cat");
        // 结果打印的是CatService.show方法中的内容
        // 你知道为什么吗？
        animalService.show(newUrl);
 
    }
 
}
```

**█ 总结说明** 
===========

还记得上面说明篇说的吗，当 cachedAdaptiveClass==null 的时候，会返回 createAdaptiveExtensionClass 这个方法的结果。而当没有一个实现类上标注 @Adaptive 时，cachedAdaptiveClass 就为 null 了。下面我们进入 createAdaptiveExtensionClass 冒个险。

```
private Class<?> createAdaptiveExtensionClass() {
    // 这里就是创建类的字符串的
    String code = this.createAdaptiveExtensionClassCode();
    ClassLoader classLoader = findClassLoader();
    // 又是getAdaptiveExtension，仔细想想这里会获取到哪个类的实例呢?
    // 提示：看看接口Compiler的实现类中是否有标注了@Adaptive，如果有就是他了
    // 其实想想一定会有实现类标注注解的。不然又会进入createAdaptiveExtensionClass方法，这不死循环了嘛。
    Compiler compiler = (Compiler)getExtensionLoader(Compiler.class).getAdaptiveExtension();
    // 根据字符串生成Class对象
    return compiler.compile(code, classLoader);
}
```

大家还是否记得，一开始我说的 @Adaptive 只能作用于两个地方。标注在实现类上和标注在接口方法上。既然标注在实现类上是不会进到这个方法里面的，那就表示标注在接口方法的时候就会进入这个方法喽。其实 **createAdaptiveExtensionClassCode** 方法，就是根据接口方法上的注解信息动态产生代码的。有兴趣的同学可以看看这个方法的源码。我这里就通过方法将 createAdaptiveExtensionClassCode 生成的类字符串打印出来给大家看看。

createAdaptiveExtensionClassCode 这个方法是私有的，我们没法直接调用它，不过可以通过反射来调用。编写测试方法。

```
ExtensionLoader<AnimalService> extensionLoader = ExtensionLoader.getExtensionLoader(AnimalService.class);
Class<? extends ExtensionLoader> extensionLoaderClass = extensionLoader.getClass();
Method method = extensionLoaderClass.getDeclaredMethod("createAdaptiveExtensionClassCode", null);
method.setAccessible(true);
Object result = method.invoke(extensionLoader, null);
System.out.println(result);
```

结果如下。下面的例子只是 Dubbo 根据 AnimalService 方法生成的类，并不是所有的类的内容都是相同的。Dubbo 会动态判断动态生成不同的结果。

```
// 包路径和接口的包路径相同
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.extension.ExtensionLoader;
 
// 类名就是接口名加$Adaptive
public class AnimalService$Adaptive implements study.rui.dubbo.AnimalService {
 
    public void run(study.rui.dubbo.Animal arg0) {
        // 前置校验，参数不能为空
        if (arg0 == null)
            throw new IllegalArgumentException("study.rui.dubbo.Animal argument == null");
        // 从参数中获取URL对象
        if (arg0.getUrl() == null)
            throw new IllegalArgumentException("study.rui.dubbo.Animal argument getUrl() == null");
 
        com.alibaba.dubbo.common.URL url = arg0.getUrl();
        // 从URL中获取参数lastName，为什么是lastName呢？
        // 因为在注解中设置了值 @Adaptive({"lastName"})
        String extName = url.getParameter("lastName");
        // URL中没有指定参数或者指定参数的值为空，抛异常
        if(extName == null)
            throw new IllegalStateException("Fail to get extension(study.rui.dubbo.AnimalService) name from url(" + url.toString() + ") use keys([lastName])");
        // 根据值获取对应的扩展实现类对象
        study.rui.dubbo.AnimalService extension = (study.rui.dubbo.AnimalService)ExtensionLoader.getExtensionLoader(study.rui.dubbo.AnimalService.class).getExtension(extName);
        // 调用具体实现类的对象的方法
        extension.run(arg0);
 
    }
 
    public void show(com.alibaba.dubbo.common.URL arg0) {
 
        if (arg0 == null)
            throw new IllegalArgumentException("url == null");
        // 如果参数就是URL类型的
        com.alibaba.dubbo.common.URL url = arg0;
        String extName = url.getParameter("firstName");
        if(extName == null)
            throw new IllegalStateException("Fail to get extension(study.rui.dubbo.AnimalService) name from url(" + url.toString() + ") use keys([firstName])");
        study.rui.dubbo.AnimalService extension = (study.rui.dubbo.AnimalService)ExtensionLoader.getExtensionLoader(study.rui.dubbo.AnimalService.class).getExtension(extName);
        extension.show(arg0);
 
    }
 
    public void say() {
        // 没有标注@Adaptive注解的方法调用就会直接抛异常
        throw new UnsupportedOperationException("method public abstract void study.rui.dubbo.AnimalService.say() of interface study.rui.dubbo.AnimalService is not adaptive method!");
    }
}
```

①@Adaptive 修饰的方法，这个方法的参数必须有一个类型是 com.alibaba.dubbo.common.URL，或者该类型中含有一个返回值是 URL，访问修饰符是 public，不是静态方法，无参的、（get 开头或方法名长度大于 3）的方法。如 Animal。将 Animal 改成下面几种情况都是可以当做参数的。Dubbo 能够校验通过，并且正确解析生成类字符串。

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
 
public class Animal {
 
    public URL getUrl() {
        return null;
    }
 
}
```

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
 
public class Animal {
 
    public URL get() {
        return null;
    }
 
}
```

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
 
public class Animal {
 
    public URL hhhh() {
        return null;
    }
 
}
```

```
package study.rui.dubbo;
 
import com.alibaba.dubbo.common.URL;
/**
 * 像这种有两个方法都返回URL的
 * 遍历所有方法，先获取到谁，就用谁去获取URL
 */
public class Animal {
 
    public URL hhhh() {
        return null;
    }
 
    public URL hhhh2() {
        return null;
    }
 
}
```

②@Adaptive 修饰的方法，符合上面条件的参数可以有多个。但只会用第一个符合条件的参数。

```
// 代码中只会使用第一个参数url，url.getParamter
@Adaptive({"firstName"})
void show(URL url, URL url2);
```

③在 @Adaptive 中没有设置 value 的时候，Dubbo 会拿到接口名根据驼峰模式，遇到大写字母加点号，将所有字母变小写。如 AnimalService 会生成 animal.service 这样的值，然后通过 url.getParameter("animal.service") 获取。

④@Adaptive 中可以指定多个值。按照顺序依次从 URL 中获取参数，直到获取到值位置。

```
// 依次从URL中获取参数名为lastName、firstName、age
// 获取到的参数值不为空就停止向后获取了。即，url.getParamter("lastName")不为空的话
// 就不会去获取lastName和age的参数值了
@Adaptive({"lastName", "firstName", "age"})
void run(Animal animal);
```

_欢迎评论指导，留下您宝贵的建议或意见，谢谢！_