# rabbitmq常用命令

# windows

```shell
#重启服务
net stop RabbitMQ && net start RabbitMQ

#####一些基本的管理命令：#####
#一步启动Erlang node和Rabbit应用：
rabbitmq-server

#在后台启动Rabbit node：
rabbitmq-server -detached

#关闭整个节点（包括应用）：
rabbitmqctl stop 
```





让配置文件生效需要重新安装服务

```
执行以下4步操作
（1）rabbitmq-service stop停止服务

（2）rabbitmq-service remove 移除服务

（3）rabbitmq-service install 按照服务

（4）rabbitmq-service start启动服务

rabbitmq-service stop
rabbitmq-service remove
rabbitmq-service install
rabbitmq-service start
```



权限控制

```bash
# the following commands create a new user for MQTT connections with full access to the default virtual host used by this plugin
rabbitmqctl add_user mqtt-test mqtt-test
rabbitmqctl set_permissions -p / mqtt-test ".*" ".*" ".*"
rabbitmqctl set_user_tags mqtt-test management
#显示有主机'/'权限的用户 以及 操作权限
rabbitmqctl list_permissions --vhost /
```

 Note that colons may not appear in usernames. 



## 常用插件

```bash
rabbitmq-plugins enable rabbitmq_management

插件

​```bash
#列出插件
rabbitmq-plugins list

#启用插件
rabbitmq-plugins enable rabbitmq_management
rabbitmq-plugins enable rabbitmq_auth_backend_http
rabbitmq-plugins enable rabbitmq_auth_backend_cache
rabbitmq-plugins enable rabbitmq_mqtt

```

