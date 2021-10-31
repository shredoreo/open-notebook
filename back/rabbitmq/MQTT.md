# MQTT

MQTT clients can interoperate with other protocols. All the functionality in the [management UI](https://www.rabbitmq.com/management.html) and several other plugins can be used with MQTT, although there may be some limitations or the need to tweak the defaults.

## [Enabling the Plugin](https://www.rabbitmq.com/mqtt.html#enabling-plugin)

The MQTT plugin is included in the RabbitMQ distribution. Before clients can successfully connect, it must be enabled using [rabbitmq-plugins](https://www.rabbitmq.com/cli.html):

```bash
rabbitmq-plugins enable rabbitmq_mqtt
```

Now that the plugin is enabled, MQTT clients will be able to connect provided that they have a set of credentials for an existing user with the appropriate permissions.

## [Local vs. Remote Client Connections](https://www.rabbitmq.com/mqtt.html#local-vs-remote)


When an MQTT client provides no login credentials, the plugin uses the guest account by default which will <u>not allow non-localhost connections</u>. When connecting from a remote host, here are the options that make sure remote clients can successfully connect:


- Create one or more new user(s), grant them full permissions to the virtual host used by the MQTT plugin and make clients that connect from remote hosts use those credentials
- Set `default_user` and `default_pass` via [MQTT plugin configuration](https://www.rabbitmq.com/mqtt.html#config) to a non-guest user who has the [appropriate permissions](https://www.rabbitmq.com/access-control.html)

### [Anonymous Connections](https://www.rabbitmq.com/mqtt.html#anonymous-connections)

MQTT supports optional authentication (clients may provide no credentials) but RabbitMQ does not. Therefore <u>a default set of credentials</u> is used for anonymous connections.

The mqtt.default_user and mqtt.default_pass configuration keys are used to specify the credentials:

```ini
mqtt.default_user = some-user
mqtt.default_pass = s3kRe7
```

It is possible to disable anonymous connections:

```ini
mqtt.allow_anonymous = false
```

If the `mqtt.allow_anonymous` key is set to false then clients **must** provide credentials.

The use of anonymous connections is **highly discouraged** and it is a subject to certain limitations (see above) enforced for a reasonable level of security by default.

## [How it Works](https://www.rabbitmq.com/mqtt.html#implementation)

RabbitMQ MQTT plugin targets MQTT 3.1.1 and supports a broad range of MQTT clients. It also makes it possible for MQTT clients to interoperate with [AMQP 0-9-1, AMQP 1.0, and STOMP](https://www.rabbitmq.com/protocols.html) clients. There is also support for multi-tenancy.

The plugin builds on top of RabbitMQ core protocol's entities: exchanges and queues. Messages published to MQTT topics use a **topic exchange** (**`amq.topic`** by default) internally. Subscribers consume from RabbitMQ **queues** bound to the topic exchange. This both enables interoperability with other protocols and makes it possible to use the [Management plugin](https://www.rabbitmq.com/management.html) to inspect queue sizes, message rates, and so on.

>  *The RabbitMQ management plugin* provides an HTTP-based API for management and monitoring of RabbitMQ nodes and clusters, along with a browser-based UI and a command line tool, [rabbitmqadmin](https://www.rabbitmq.com/management-cli.html). 

 Note that MQTT uses **slashes** ("/") for topic segment separators and AMQP 0-9-1 uses **dots**. This plugin translates patterns under the hood to bridge the two, for example, <u>cities/london becomes cities.london and **vice versa**</u>. This has one important **limitation**: <u>MQTT topics that have dots in them won't work as expected and are to be avoided, the same goes for AMQP 0-9-1 routing keys that contains slashes.</u> 

|      | topic segment separators |
| :--- | ------------------------ |
| MQTT | /                        |
| AMQP | .                        |



> ### Conclusion
>
> When an MQTT client provides no login credentials, the plugin uses the guest account by default which will <u>not allow non-localhost connections</u>
>
> Create one or more new user(s) with full permision to the vhost,and Set default_user and default_pass
>
> Messages published to MQTT topics use a **topic exchange** (`amq.topic` by default) internally. Subscribers consume from RabbitMQ **queues** bound to the topic exchange



### [Subscription Durability](https://www.rabbitmq.com/mqtt.html#durability)

MQTT 3.1 assumes two primary usage scenarios:

- Transient clients that use *transient (non-persistent) messages*
- Stateful clients that use *durable subscriptions (non-clean sessions, QoS1)*

### QoS

This section briefly covers how these scenarios map to RabbitMQ queue *durability* and *persistence* features.

**Transient (QoS0)** subscription use non-durable, auto-delete queues that will be deleted when the client disconnects.

**Durable (QoS1)** subscriptions use durable queues. Whether the queues are auto-deleted is controlled by the client's **clean session flag**. Clients with clean sessions use auto-deleted queues, others use non-auto-deleted ones.



 Queues created for MQTT subscribers will have names starting with **`mqtt-subscription-`**, one per subscription QoS level. The queues will have [queue TTL](https://www.rabbitmq.com/ttl.html) depending on MQTT plugin configuration, 24 hours by default. 

 **RabbitMQ does not support QoS2 subscriptions**. RabbitMQ automatically **downgrades** QoS 2 publishes and subscribes to QoS 1. Messages published as QoS 2 will be sent to subscribers as QoS 1. Subscriptions with QoS 2 will be downgraded to QoS1 during SUBSCRIBE request (SUBACK responses will contain the actually provided QoS level). 

## [Plugin Configuration](https://www.rabbitmq.com/mqtt.html#config)

Here is a sample [configuration](https://www.rabbitmq.com/configure.html#config-file) that demonstrates a number of MQTT plugin settings:

```ini
mqtt.listeners.tcp.default = 1883
## Default MQTT with TLS port is 8883
# mqtt.listeners.ssl.default = 8883

# anonymous connections, if allowed, will use the default
# credentials specified here
mqtt.allow_anonymous  = true
mqtt.default_user     = guest
mqtt.default_pass     = guest

mqtt.vhost            = /
mqtt.exchange         = amq.topic
# 24 hours by default
mqtt.subscription_ttl = 86400000
mqtt.prefetch         = 10
```







### TCP Listener Options

The plugin supports TCP listener option configuration.

The settings use a common prefix, mqtt.tcp_listen_options, and control things such as TCP buffer sizes, inbound TCP connection queue length, whether [TCP keepalives](https://www.rabbitmq.com/heartbeats.html#tcp-keepalives) are enabled and so on. See the [Networking guide](https://www.rabbitmq.com/networking.html) for details.

```ini
mqtt.listeners.tcp.1 = 127.0.0.1:1883
mqtt.listeners.tcp.2 = ::1:1883

mqtt.tcp_listen_options.backlog = 4096
mqtt.tcp_listen_options.recbuf  = 131072
mqtt.tcp_listen_options.sndbuf  = 131072

mqtt.tcp_listen_options.keepalive = true
mqtt.tcp_listen_options.nodelay   = true

mqtt.tcp_listen_options.exit_on_close = true
mqtt.tcp_listen_options.send_timeout  = 120
```



### [TLS Support](https://www.rabbitmq.com/mqtt.html#tls)

To use TLS for MQTT connections, [TLS must be configured](https://www.rabbitmq.com/ssl.html) in the broker. To enable TLS-enabled MQTT connections, add a TLS listener for MQTT using the mqtt.listeners.ssl.* configuration keys.

The plugin will use core RabbitMQ server certificates and key (just like AMQP 0-9-1 and AMQP 1.0 listeners do):

```ini
ssl_options.cacertfile = /path/to/ca_certificate.pem
ssl_options.certfile   = /path/to/server_certificate.pem
ssl_options.keyfile    = /path/to/server_key.pem
ssl_options.verify     = verify_peer
ssl_options.fail_if_no_peer_cert  = true

# default TLS-enabled port for MQTT connections
mqtt.listeners.ssl.default = 8883
mqtt.listeners.tcp.default = 1883
```

 Note that RabbitMQ rejects SSLv3 connections by default because that protocol is known to be compromised. 



### [Virtual Hosts](https://www.rabbitmq.com/mqtt.html#virtual-hosts)

RabbitMQ is a multi-tenant system at the core and every connection belongs to a virtual host. Some messaging protocols have the concept of vhosts, others don't. MQTT falls into the latter category. Therefor the MQTT plugin needs to provide a way to map connections to vhosts.

The `vhost` option controls which RabbitMQ vhost the adapter connects to by default. The `vhost` configuration is only consulted if no vhost is provided during connection establishment. There are several (optional) ways to specify the vhost the client will connect to.

#### Port to Virtual Host Mapping

First way is mapping MQTT plugin (TCP or TLS) listener ports to vhosts. The mapping is specified thanks to the **`mqtt_port_to_vhost_mapping`** [global runtime parameter](https://www.rabbitmq.com/parameters.html). Let's take the following plugin configuration:





##  reflect

> 遇到的问题：
>
> 修改配置文件后，需要重启节点，而不是应用。不然会崩溃。使用如下命令
>
> `rabbitmq-server`
>
> 默认的端口是：tcp:1883  \  SSL:8883
>
>  `mqtt.allow_anonymous` 如果为false，需要提供默认的账号和密码，guest账户只允许本地访问。若为true，可以不提供，





 MQTT订阅通配符受到限制，因为它们只能显示为后缀。AMQP主题不受这种限制，因此通配符可以出现在任何位置。MQTT适配器实现了更灵活的AMQP模式，但使用了MQTT语法。 



