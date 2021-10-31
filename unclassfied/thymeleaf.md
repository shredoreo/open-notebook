# 打开cmd

输入：
file:///C:/windows/system32/cmd.exe
 或者
C:/windows/system32/cmd.exe



mongodb

```
开启服务：
mongod.exe --dbpath "d:/data/db"
```





# JPA

###FetchType

1、FetchType.LAZY：懒加载，加载一个实体时，定义懒加载的属性不会马上从数据库中加载。

2、FetchType.EAGER：急加载，加载一个实体时，定义急加载的属性会立即从数据库中加载。

3、比方User类有两个属性，name跟address，就像百度知道，登录后用户名是需要显示出来的，此属性用到的几率极大，要马上到数据库查，用急加载；而用户地址大多数情况下不需要显示出来，只有在查看用户资料是才需要显示，需要用了才查数据库，用懒加载就好了。所以，并不是一登录就把用户的所有资料都加载到对象中，于是有了这两种加载模式。  

### CascadeType

- CascadeType.PERSIST
  官方文档的说明：Cascade persist operation
  看到网上很多博客对这一枚举值的解释是：级联持久化（保存）操作（持久保存拥有方实体时，也会持久保存该实体的所有相关数据。）
  我的内心 OS 是：妈蛋。我也知道是级联 persist 操作啊关键是怎么操作啊。妈蛋。拥有方实体是个什么玩意儿，该实体又是个什么玩意儿。
  经过实践检验，我的理解是：**给当前设置的实体操作另一个实体的权限。**这个理解可以推广到每一个 CascadeType。因此，其余 CascadeType 枚举值将不再一一详细解释。
  For example:

```
public class Student {
    @ManyToMany(cascade=CascadeType.PERSIST,fetch=FetchType.LAZY)
    private Set<Course> courses = new HashSet<>();
    //其他代码略。
}
```

可以看到，我们在上面的代码中给了 Student 对 Course 进行级联保存（cascade=CascadeType.PERSIST）的权限。此时，若 Student 实体持有的 Course 实体在数据库中不存在时，保存该 Student 时，系统将自动在 Course 实体对应的数据库中保存这条 Course 数据。而如果没有这个权限，则无法保存该 Course 数据。

- CascadeType.REMOVE
  Cascade remove operation，级联删除操作。
  删除当前实体时，与它有映射关系的实体也会跟着被删除。

- CascadeType.MERGE
  Cascade merge operation，级联更新（合并）操作。
  当 Student 中的数据改变，会相应地更新 Course 中的数据。

- CascadeType.DETACH
  Cascade detach operation，级联脱管 / 游离操作。
  如果你要删除一个实体，但是它有外键无法删除，你就需要这个级联权限了。它会撤销所有相关的外键关联。

  > 经常我们在删除 DB 记录时，会为外键关联而无法删除数据感到苦恼。这里个人经常用到的一个方法就是，先让关联主键失效，然后再删除数据，数据删除完成后，再让其主
  >
  > 键生效，这样很好的解决了删除级联数据难的问题。
  >
  > 第一步：让主键失效：alter table table_name disable primary key cascade;
  >
  > 第二步：删除数据：delete table_name;
  >
  > 第三步：让主键生效：alter table table_name enable primary key;
  >
  > https://blog.csdn.net/gzw1623231307/article/details/56474676

- CascadeType.REFRESH
  Cascade refresh operation，级联刷新操作。
  假设场景 有一个订单, 订单里面关联了许多商品, 这个订单可以被很多人操作, 那么这个时候 A 对此订单和关联的商品进行了修改, 与此同时, B 也进行了相同的操作, 但是 B 先一步比 A 保存了数据, 那么当 A 保存数据的时候, 就需要先刷新订单信息及关联的商品信息后, 再将订单及商品保存。(来自[良心会痛](https://www.jianshu.com/users/a11a21634ee8/timeline)的评论)

- CascadeType.ALL
  Cascade all operations，清晰明确，拥有以上所有级联操作权限。

### get 与 find

JPA 中通过 primaryKey 来得到实体对象可以通过一个连个方法find(), getRefrence();

 这两个方法相当于 Hibernate 中的 get() 和 load() 方法。这两个方法的区别跟 get() 和 load() 方法的区别是一样的，其中 hibernate 中 get 方法和 load 方法的根本区别在于：

如果你使用 load 方法，hibernate 认为该 id 对应的对象（数据库记录）在数据库中是一定存在的，所以它可以放心的使用，它可以放心的使用代理来延迟加载该对象。在用到对象中的其他属性数据时才查询数据库，但是万一数据库中不存在该记录，那没办法，只能抛异常，所说的 load 方法抛异常是指在使用该对象的数据时，数据库中不存在该数据时抛异常，而不是在创建这个对象时。由于 session 中的缓存对于 hibernate 来说是个相当廉价的资源，所以在 load 时会先查一下 session 缓存看看该 id 对应的对象是否存在，不存在则创建代理。所以如果你知道该 id 在数据库中一定有对应记录存在就可以使用 load 方法来实现延迟加载。

对于 get 方法，hibernate 会确认一下该 id 对应的数据是否存在，首先在 session 缓存中查找，然后在二级缓存中查找，还没有就查数据库，数据库中没有就返回 null。

一句话，hibernate 对于 load 方法认为该数据在数据库中一定存在，可以放心的使用代理来延迟加载，如果在使用过程中发现了问题，只能抛异常；而对于 get 方法，hibernate 一定要获取到真实的数据，否则返回 null。





# controller

页面跳转、实际的业务操作，这两个需要分开吗。

使用ResponseEntity作为返回



# 博客管理

## 导入依赖：

```
	compile('es.nitaur.markdown:txtmark:0.16')

```







# 权限

    User.class
    
    @ManyToMany(cascade = CascadeType.DETACH, fetch = FetchType.EAGER)
    @JoinTable(name = "user_authority", joinColumns = @JoinColumn(name = "user_id", referencedColumnName = "id"),
            inverseJoinColumns = @JoinColumn(name = "authority_id", referencedColumnName = "id"))
    private List<Authority> authorities;


#

value object是什么





```
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)//启用方法级别安全设置
public class SecurityConfig extends WebSecurityConfigurerAdapter {
```







##CSRF防护

### 一、什么是csrf攻击

CSRF（Cross-site request forgery）跨站请求伪造，也被称为“One Click Attack”或者Session Riding，通常缩写为CSRF或者XSRF，是一种对网站的恶意利用。尽管听起来像跨站脚本（[XSS](https://baike.baidu.com/item/XSS)），但它与XSS非常不同，XSS利用站点内的信任用户，而CSRF则通过伪装成受信任用户的请求来利用受信任的网站。

比如：

> 攻击通过在授权用户访问的页面中包含链接或者脚本的方式工作。
>
> 如：一个网站用户Bob可能正在浏览聊天论坛，而同时另一个用户Alice也在此论坛中，并且后者刚刚发布了一个具有Bob银行链接的图片消息。设想一下，Alice编写了一个在Bob的银行站点上进行取款的form提交的链接，并将此链接作为图片src。如果Bob的银行在cookie中保存他的授权信息，并且此cookie没有过期，那么当Bob的浏览器尝试装载图片时将提交这个取款form和他的cookie，这样在没经Bob同意的情况下便授权了这次事务。
>
> ![](D:\ProgramData\md\csrf.jpg)



### 二、springSecurity怎么防范csrf？

thyemleaf支持表单的防范。

```
// 添加 thymeleaf Spring security
	compile('org.thymeleaf.extras:thymeleaf-extras-springsecurity4:3.0.2.RELEASE')
```

会验证请求，若请求中不带token，那么请求会被拒绝。

thymeleaf中对于表单，会自动携带CSRF的token。

但是其他的请求，比如post、delete，thymeleaf无法处理，只能用户自己处理。

使用方法：

```javascript
在公共fragment，如header加入：
    <!-- CSRF -->
	<meta name="_csrf" th:content="${_csrf.token}"/>
	<!-- default header name is X-CSRF-TOKEN -->
	<meta name="_csrf_header" th:content="${_csrf.headerName}"/>
        
===========================	
在js代码中，加入,将meta的信息取过来，并在ajax的beforeSend加入头部信息。例如：
	$("#rightContainer").on("click",".blog-delete-user", function () {
		// 获取 CSRF Token
		var csrfToken = $("meta[name='_csrf']").attr("content");
		var csrfHeader = $("meta[name='_csrf_header']").attr("content");

		$.ajax({
			url: "/users/" + $(this).attr("userId") ,
			type: 'DELETE',
			beforeSend: function(request) {
				request.setRequestHeader(csrfHeader, csrfToken); // 添加  CSRF Token
			},

```







#工具类

	// 添加  Apache Commons Lang 依赖
	compile('org.apache.commons:commons-lang3:3.5')
#Spring Security

## 认证

认证authentication ：建立主体的过程。主体指可以在应用程序执行操作的用户、设备或其他系统

授权authorization：决定是否允许主体在程序中 进行操作

身份验证技术

## 依赖

```
	// 添加spring security
	compile('org.springframework.boot:spring-boot-starter-security')
	
	// 添加 thymeleaf Spring security
	compile('org.thymeleaf.extras:thymeleaf-extras-springsecurity4:3.0.2.RELEASE')

```

## 实现

1、编写配置类

```java
1、编写配置类
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
                .antMatchers("/css/**","/js/**","/fonts/**","/index").permitAll()
                .antMatchers("/users/**").hasRole("ADMIN")
                .and()
                .formLogin()
                .loginPage("/login").failureUrl("/login-error");

    }

    @Autowired
    public void configGlobal(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
                .withUser("aaa").password("aaa").roles("ADMIN");
    }
}

2、编写控制器
注意：不需要对/login页面进行映射，因为security的配置中已经做了映射。

3、编写前端页面
	加上命名空间：
 		xmlns:th="http://www.thymeleaf.org"
      xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
      xmlns:sec="http://www.thymeleaf.org/thymeleaf-extras-springsecurity4">
<div class="container blog-content-container">
    <div sec:authorize="isAuthenticated()">
        <p>已有用户登录!</p>
        <p>the login user is：<span sec:authentication="name"></span> </p>
        <p>the role is:<span sec:authentication="principal.authorities" ></span></p>
    </div>
    <div sec:authorize="isAnonymous()">
        <p>未登录。</p>
    </div>
</div>

```







#项目分析

## 需求分析



## 原型设计

- 提供给用户，尽早让客户确认最终的产品效果。
- 指导开发。因为界面是难以用语言描述的，使用原型让开发者指导要开发什么功能。
- 提高开发效率。可以在原型的基础上进行开发，提高效率。
- 



# bootstrap

normalize.css



## 网格系统

什么是移动设备优先？

- 继承的css是移动优先。
- 媒体查询
- 渐进增强。随着屏幕的大小增加元素数目

响应式：12列



# 架构

## 三层架构

- 表示层

- 业务层

- 数据访问层

  



# ElasticSearch全文搜索

非结构化数据的检索

- 顺序扫描法（serial scanning）：从头扫到尾，如window文件搜索。适用于文件数量少的情况。
- 全文搜索法：将非结构化数据的部分信息，转为结构化数据，然后创建索引，再搜索。

## 全文检索

### 原理

建立文本库

建立索引

执行搜索

过滤结果

基于java的开源实现

lucene（引擎）

ElasticSearch（建立在lucene上）：只支持json数据格式、实时搜索效率更高

Solr：利用zookeeper进行分布式管理，支持多种数据格式、功能多、

ElasticSearch

- 高度可扩展的开源全文搜索和分析引起
- 快速、近实时对大数据进行存储、搜索和分析
- 用于支撑复杂的数据搜索需求的企业级应用

特点：

- 分布式：把数据索引分片到
- 近实时：index_refresh_interval
- 集群：
- 节点：服务器节点
- 索引：
- 类型：
- 文档：
- 分片：将索引分成小片
- 副本：对分片设置副本，

## 依赖\配置

1. elasticsearch
2. spring data elasticsearch
3. jna

		​	// 添加  Spring Data Elasticsearch 的依赖
	​	compile('org.springframework.boot:spring-boot-starter-data-elasticsearch')
	​	// 添加  JNA 的依赖
	​	compile('net.java.dev.jna:jna:4.3.0')

```
# 内嵌 Elasticsearch 实例。默认存储位置是工作目录的 elastic 目录
spring.data.elasticsearch.properties.path.home=target/elastic
# 设置连接超时时间
spring.data.elasticsearch.properties.transport.tcp.connect_timeout=120s
```



## 使用ES

一、新建实体类，让其符合Jpa规范。

二、编写Repository接口，继承ElasticsearchRepository<EsBlog,String>，编写自定义方法。

```java
@Repository
public interface EsBlogRepository extends ElasticsearchRepository<EsBlog,String> {

    Page<EsBlog> findDistinctByTitleContainingOrSummaryContainingOrContentContaining(String title, String summary, String content, Pageable pageable);
}

```

三、编写测试类。

```java
 @Before//在测试代码前执行。
    public void initRepository(){
        esBlogRepository.deleteAll();
        esBlogRepository.save(new EsBlog("登黄鹤楼","王之涣的登黄鹤楼",
                "白日依山尽，黄河入海流，欲穷千里目，更上一层楼。"));
        esBlogRepository.save(new EsBlog("相思","王维的相思",
                "红豆生南国，春来发几枝。愿君多采撷，此物最相思。"));
        esBlogRepository.save(new EsBlog("静夜思","李白",
                "床前明月光，疑是地上霜。举头望明月，低头思故乡。"));

    }

    @Test
    public void testFindDistinctByTitleContainingOrSummaryContainingOrContentContaining() {
        Pageable pageable = new PageRequest(0,20);
        String title = "思";
        String summary = "相思";
        String content = "相思";
        Page<EsBlog> page = esBlogRepository.findDistinctByTitleContainingOrSummaryContainingOrContentContaining(title, summary, content, pageable);
        assertThat(page.getTotalElements()).isEqualTo(2);

        for (EsBlog blog : page.getContent()){
            System.out.println(blog);
        }

        System.out.println("---------------------");
        title = "相思";
        page = esBlogRepository.findDistinctByTitleContainingOrSummaryContainingOrContentContaining(title, summary, content, pageable);
        assertThat(page.getTotalElements()).isEqualTo(1);
        for (EsBlog blog : page.getContent()){
            System.out.println(blog);
        }
    }
```

四、编写controller

五、运行springboot，查看结果。

# spring data jap

添加依赖

```
		// 添加 Spring Data JPA 的依赖
	compile('org.springframework.boot:spring-boot-starter-data-jpa')
	
	// 添加 MySQL连接驱动 的依赖
	compile('mysql:mysql-connector-java:6.0.5')

	
		// 自定义  Hibernate 的版本
	ext['hibernate.version'] = '5.2.8.Final'
	
```

![1564460823156](D:\ProgramData\md\1564460823156.png)

出现以上错误，需要内嵌数据库。这里使用H2

```
	//H2
	runtime ('com.h2database:h2:1.4.193')

```



1. 修改实体，加上相应的注解、主键生成策略、protected修饰构造器、重写tostring方法

2. 修改dao，接口继承

   ```
   public interface UserRepository extends CrudRepository<User,Long> {
   
   }
   ```

3. 修改Controller，直接使用CrudRepository提供的方法。

4. 开启H2控制台，

   ```
   spring.h2.console.enabled=true
   ```

   ![1564464038498](D:\ProgramData\md\1564464038498.png)

5. 在http://localhost:8080/h2-console查看。

6. 整合mysql、jpa

   ```
   #mysql
   spring.datasource.url=jdbc:mysql://localhost/blog?characterEncoding=utf-8&useSSL=false&serverTimezone=UTC 
   spring.datasource.username=root
   spring.datasource.password=root
   spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
   
   # JPA
   spring.jpa.show-sql = true
   spring.jpa.hibernate.ddl-auto=create-drop
   
   ```

7. 创建数据库

   ```sql
    create database blog default charset utf8 collate utf8_general_ci;
   ```

   

8. 启动项目。



# thymeleaf

## 使用技巧

@RequestBody在使用时，前台需要JSON.Stringnify()封装数据,而且需要使用post请求，因为get请求没有请求体，且@RequestBody 接收请求体中的 json 数据；
若不加注解接收 URL 中的数据并组装为对象。

在controller中，可以使用`return "/userspace/u :: #catalogRepleace";`返回页面的一部分，该部分为：`/userspace/u.html`中` id `为`catalogRepleace `的那个元素及其内容。而且是经过渲染的。



![1564404063144](D:\ProgramData\md\1564404063144.png )

### 变量表达式

### 消息表达式#{...}

也称为文本外部化、国际化i18n

### 选择表达式*{、、、}

在当前选择的对象下执行的，表示选择变量某个属性。

用于遍历的时候在某个

###连接表达式@{}

### 分段表达式 th:insert

### 字面量

‘xxx’ 表示字符串

### 无操作 _

### 代码

```
新建domain.User
User{
name；
id；

}
UserRepository{
saveOrUpdate(User)
deleteUser(id)
getUserById(id)
listUsers()
}

UserRepositoryImpl{
使用concurrentHashMap 保存用户列表
使用原子类，确保id是唯一且递增的。
}

UserController

```





# gradle

build.gradle是gradle项目的构建配置文件。



gradlewrapper

在gradle/wrapper/gradlewrapper.properties中修改配置。

window下：gradlew.bat

linux下：gradlew

gradle bootRun可以运行java程序。

gradlew bootRun也可以。

#测试

##springboot使用MockMvc来测试：

在测试类上加注解 @AutoConfigMockMvc

```java
@RunWith(SpringRunner.class)
@SpringBootTest(classes = Application.class)
@AutoConfigureMockMvc
public class HelloControllerTests {

	@Autowired
	private MockMvc mockMvc;

	@Test
	public void testHello() throws Exception {
		mockMvc.perform(MockMvcRequestBuilders.get("/hello").accept(MediaType.APPLICATION_JSON))
				.andExpect(status().isOk())
				.andExpect(content().string(equalTo("Hello world!")));

	}

}

```



