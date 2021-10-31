一、**编程题**

1）基于SpringBoot实现一个登陆功能（含有登录拦截验证）

2）使用Spring Session进行Session一致性控制

3）将工程打成war包

4）将war包部署到tomcat集群中，要求1个Nginx节点、2个Tomcat节点

​    请求 —> Nginx（轮询策略） —> Tomcat1 / Tomcat2

5）完成测试

**作业具体要求**

注意：作业提交时提交可运行的工程代码（源代码和war包）以及sql脚本，Nginx配置及Tomcat配置，Redis配置统一修改为：

redis.host=localhost

redis.port=6379

redis.connectionTimeout=5000

redis.password=

redis.database=0

**作业资料说明：**

1、提供资料：工程代码和代码war包，sql脚本、Nginx配置文件、Tomcat配置文件、Redis配置文件，功能演示和原理讲解视频。 

2、讲解内容包含：题目分析、实现思路、代码讲解。   

3、效果视频验证：实现Nginx轮询Tomcat1、Tomcat2，对代码工程进行Session一致性控制。