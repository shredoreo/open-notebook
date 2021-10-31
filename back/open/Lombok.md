> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [blog.csdn.net](https://blog.csdn.net/ThinkWon/article/details/101392808)

### 文章目录

*   *   [1.Lombok 简介](#1Lombok_1)
    *   [2.Lombok 使用](#2Lombok_11)
    *   *   [2.1 添加 maven 依赖](#21maven_15)
        *   [2.2 安装插件](#22_26)
        *   [2.3 解决编译时出错问题](#23_33)
        *   [2.4 示例](#24_39)
        *   [2.5 常用注解](#25_168)
    *   [3.Lombok 工作原理](#3Lombok_182)
    *   [4.Lombok 的优缺点](#4Lombok_218)

1.Lombok 简介
-----------

> 官方介绍  
> Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java.Never write another getter or equals method again, with one annotation your class has a fully featured builder, Automate your logging variables, and much more.

大概的意思：Lombok 是一个 Java 库，能自动插入编辑器并构建工具，简化 Java 开发。通过添加注解的方式，不需要为类编写 getter 或 eques 方法，同时可以自动化日志变量。[官网链接](https://www.projectlombok.org/)

简而言之：Lombok 能以简单的注解形式来简化 java 代码，提高开发人员的开发效率。  
[博客及源码 GitHub 链接](https://github.com/JourWon/test-lombok)

2.Lombok 使用
-----------

使用 Lombok 需要的开发环境 **Java+Maven+IntelliJ IDEA 或者 Eclipse(安装 Lombok Plugin)**

### 2.1 添加 maven 依赖

```
<dependency>
	<groupId>org.projectlombok</groupId>
	<artifactId>lombok</artifactId>
	<version>1.18.4</version>
	<scope>provided</scope>
</dependency>
```

### 2.2 安装插件

使用 Lombok 还需要插件的配合，我使用开发工具为 idea，这里只讲解 idea 中安装 lombok 插件，使用 eclipse 和 myeclipse 的小伙伴和自行 google 安装方法。  
打开 idea 的设置，点击 **Plugins**，点击 **Browse repositories**，在弹出的窗口中搜索 **lombok**，然后安装即可。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL2xvbWJvay8lRTUlQUUlODklRTglQTMlODVsb21ib2slRTYlOEYlOTIlRTQlQkIlQjYucG5n?x-oss-process=image/format,png)

### 2.3 解决编译时出错问题

编译时出错，可能是没有 enable 注解处理器。`Annotation Processors > Enable annotation processing`。设置完成之后程序正常运行。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL2xvbWJvay8lRTUlQkMlODAlRTUlOTAlQUYlRTYlQjMlQTglRTglQTclQTMlRTklODUlOEQlRTclQkQlQUUucG5n?x-oss-process=image/format,png)

### 2.4 示例

下面举两个栗子，看看使用 lombok 和不使用的区别。

创建一个用户类

**不使用 Lombok**

```
public class User implements Serializable {

    private static final long serialVersionUID = -8054600833969507380L;

    private Integer id;

    private String username;

    private Integer age;

    public User() {
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", user + username + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        User user = (User) o;
        return Objects.equals(id, user.id) &&
                Objects.equals(username, user.username) &&
                Objects.equals(age, user.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, username, age);
    }

}
```

**使用 Lombok**

```
@Data
public class User implements Serializable {

    private static final long serialVersionUID = -8054600833969507380L;

    private Integer id;

    private String username;

    private Integer age;

}
```

编译源文件，然后反编译 class 文件，反编译结果如下图。说明 @Data 注解在类上，会为类的所有属性自动生成 setter/getter、equals、canEqual、hashCode、toString 方法，如为 final 属性，则不会为该属性生成 setter 方法。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL2xvbWJvay8lRTUlOEYlOEQlRTclQkMlOTYlRTglQUYlOTElRTclOTQlQTglRTYlODglQjclRTclQjElQkIucG5n?x-oss-process=image/format,png)

自动化日志变量

```
@Slf4j
@RestController
@RequestMapping(("/user"))
public class UserController {

    @GetMapping("/getUserById/{id}")
    public User getUserById(@PathVariable Integer id) {
        User user = new User();
        user.setUsername("风清扬");
        user.setAge(21);
        user.setId(id);

        if (log.isInfoEnabled()) {
            log.info("用户 {}", user);
        }

        return user;
    }

}
```

通过反编译可以看到 @Slf4j 注解生成了 log 日志变量（严格意义来说是常量），无需去声明一个 log 就可以在类中使用 log 记录日志。

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL2xvbWJvay8lRTUlOEYlOEQlRTclQkMlOTYlRTglQUYlOTElRTclOTQlQTglRTYlODglQjdjb250cm9sbGVyJUU3JUIxJUJCLnBuZw?x-oss-process=image/format,png)

### 2.5 常用注解

下面介绍一下常用的几个注解：

*   **@Setter** 注解在类或字段，注解在类时为所有字段生成 setter 方法，注解在字段上时只为该字段生成 setter 方法。
*   **@Getter** 使用方法同上，区别在于生成的是 getter 方法。
*   **@ToString** 注解在类，添加 toString 方法。
*   **@EqualsAndHashCode** 注解在类，生成 hashCode 和 equals 方法。
*   **@NoArgsConstructor** 注解在类，生成无参的构造方法。
*   **@RequiredArgsConstructor** 注解在类，为类中需要特殊处理的字段生成构造方法，比如 final 和被 @NonNull 注解的字段。
*   **@AllArgsConstructor** 注解在类，生成包含类中所有字段的构造方法。
*   **@Data** 注解在类，生成 setter/getter、equals、canEqual、hashCode、toString 方法，如为 final 属性，则不会为该属性生成 setter 方法。
*   **@Slf4j** 注解在类，生成 log 变量，严格意义来说是常量。private static final Logger log = LoggerFactory.getLogger(UserController.class);

3.Lombok 工作原理
-------------

在 Lombok 使用的过程中，只需要添加相应的注解，无需再为此写任何代码。自动生成的代码到底是如何产生的呢？

核心之处就是对于注解的解析上。JDK5 引入了注解的同时，也提供了两种解析方式。

*   运行时解析

运行时能够解析的注解，必须将 @Retention 设置为 RUNTIME，这样就可以通过反射拿到该注解。java.lang.reflect 反射包中提供了一个接口 AnnotatedElement，该接口定义了获取注解信息的几个方法，Class、Constructor、Field、Method、Package 等都实现了该接口，对反射熟悉的朋友应该都会很熟悉这种解析方式。

*   编译时解析

编译时解析有两种机制，分别简单描述下：

1）Annotation Processing Tool

apt 自 JDK5 产生，JDK7 已标记为过期，不推荐使用，JDK8 中已彻底删除，自 JDK6 开始，可以使用 Pluggable Annotation Processing API 来替换它，apt 被替换主要有 2 点原因：

*   api 都在 com.sun.mirror 非标准包下
*   没有集成到 javac 中，需要额外运行

2）Pluggable Annotation Processing API

[JSR 269](https://jcp.org/en/jsr/detail?id=269) 自 JDK6 加入，作为 apt 的替代方案，它解决了 apt 的两个问题，javac 在执行的时候会调用实现了该 API 的程序，这样我们就可以对编译器做一些增强，javac 执行的过程如下：

![](https://imgconvert.csdnimg.cn/aHR0cHM6Ly9yYXcuZ2l0aHVidXNlcmNvbnRlbnQuY29tL0pvdXJXb24vaW1hZ2UvbWFzdGVyL2xvbWJvay9sb21ib2slRTUlQjclQTUlRTQlQkQlOUMlRTUlOEUlOUYlRTclOTAlODYucG5n?x-oss-process=image/format,png)

Lombok 本质上就是一个实现了 “[JSR 269 API](https://www.jcp.org/en/jsr/detail?id=269)” 的程序。在使用 javac 的过程中，它产生作用的具体流程如下：

1.  javac 对源代码进行分析，生成了一棵抽象语法树（AST）
2.  运行过程中调用实现了 “JSR 269 API” 的 Lombok 程序
3.  此时 Lombok 就对第一步骤得到的 AST 进行处理，找到 @Data 注解所在类对应的语法树（AST），然后修改该语法树（AST），增加 getter 和 setter 方法定义的相应树节点
4.  javac 使用修改后的抽象语法树（AST）生成字节码文件，即给 class 增加新的节点（代码块）

通过读 Lombok 源码，发现对应注解的实现都在 HandleXXX 中，比如 @Getter 注解的实现在 HandleGetter.handle()。还有一些其它类库使用这种方式实现，比如 [Google Auto](https://github.com/google/auto)、[Dagger](http://square.github.io/dagger/) 等等。

4.Lombok 的优缺点
-------------

**优点：**

1.  能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString 等方法，提高了一定的开发效率
2.  让代码变得简洁，不用过多的去关注相应的方法
3.  属性做修改时，也简化了维护为这些属性所生成的 getter/setter 方法等

**缺点：**

1.  不支持多种参数构造器的重载
2.  虽然省去了手动创建 getter/setter 方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度