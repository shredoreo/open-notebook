rabbitmq linux、

```bash
#启动服务
rabbitmq-server start &

#启动时可能出现服务已启动，需要杀进程


#查看rabbit进程
ps -ef|grep rabbit

#杀进程
kill xxx

#验证是否启动
lsof -i:5672


#停止服务
rabbitmqctl stop_app
#or
rabbitmq-server stop &
```



## 插件

```bash
#查看所有插件
rabbitmq-plugins list

#安装管控台
rabbitmq-plugins enable rabbitmq_management
```

命令行終端操作，在控制台中都有体现。

## rabbitmqctl

```bash
#关闭应用
rabbitmqctl stop_app

#启动应用
rabbitmqctl start_app

#节点状态
rabbitmqctl status

```





```bash
vim /etc/hostname#查看主机名
```







rabbitmq-server start &