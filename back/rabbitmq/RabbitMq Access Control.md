# RabbitMq Access Control

> [from rabbitmq](https://www.rabbitmq.com/access-control.html)



## [Authentication: Who Do You Say You Are?](https://www.rabbitmq.com/access-control.html#authentication)

 With that identity, RabbitMQ nodes can look up its permissions and [authorize](https://www.rabbitmq.com/access-control.html#authorisation) access to **resources** such as [virtual hosts](https://www.rabbitmq.com/vhosts.html), queues, exchanges, and so on. 

Two primary ways of authenticating a client are **[username/password pairs](https://www.rabbitmq.com/passwords.html)** and **[X.509 certificates](https://en.wikipedia.org/wiki/X.509)**. Username/password pairs can be used with a variety of [authentication backends](https://www.rabbitmq.com/access-control.html#backends) that verify the credentials.

Connections that fail to authenticate will be closed with an error message in the [server log](https://www.rabbitmq.com/logging.html).

To authenticate client connections using X.509 certificate a built-in plugin, [rabbitmq-auth-mechanism-ssl](https://github.com/rabbitmq/rabbitmq-auth-mechanism-ssl), must be enabled and clients must be [configured to use the EXTERNAL mechanism](https://www.rabbitmq.com/access-control.html#mechanisms). With this mechanism, any client-provided password will be ignored.

## ["guest" user can only connect from localhost](https://www.rabbitmq.com/access-control.html#loopback-users)

By default, the guest user is **prohibited** from connecting from remote hosts; it can only connect over a loopback interface (i.e. localhost). This applies to [connections regardless of the protocol](https://www.rabbitmq.com/connections.html). Any other users will not (by default) be restricted in this way.

The recommended way to address this in production systems is to create a new user or set of users with the permissions to access the necessary virtual hosts. This can be done using [CLI tools](https://www.rabbitmq.com/cli.html), [HTTP API or definitions import](https://www.rabbitmq.com/management.html).

It is possible to allow the guest user to connect from a remote host by setting the loopback_users configuration to none.

A minimalistic [RabbitMQ config file](https://www.rabbitmq.com/configure.html) which allows remote connections for guest looks like so:

```ini
# DANGER ZONE!
#
# allowing remote connections for default user is highly discouraged
# as it dramatically decreases the security of the system. Delete the user
# instead and create a new one with generated secure credentials.
loopback_users = none
```

## [Authorisation: How Permissions Work](https://www.rabbitmq.com/access-control.html#authorisation)



 RabbitMQ distinguishes between ***configure*, *write* and *read* operations** on a resource. 
The *configure* operations create or destroy resources, or alter their behaviour. 
The *write* operations inject messages into a resource. And 
the *read* operations retrieve messages from a resource. 





### [User Tags and Management UI Access](https://www.rabbitmq.com/access-control.html#user-tags)

In addition to the permissions covered above, users can have tags associated with them. Currently only management UI access is controlled by **user tags**.

The tags are managed using [rabbitmqctl](https://www.rabbitmq.com/rabbitmqctl.8.html#set_user_tags). Newly created users do not have any tags set on them by default.

## [Alternative Authentication and Authorisation Backends](https://www.rabbitmq.com/access-control.html#backends)

Authentication and authorisation are pluggable. Plugins can provide implementations of

- authentication ("authn") backends
- authorisation ("authz") backends

It is possible for a plugin to provide both. For example the internal, [LDAP](https://www.rabbitmq.com/ldap.html) and [HTTP](https://github.com/rabbitmq/rabbitmq-auth-backend-http) backends do so.

### [Combining Backends](https://www.rabbitmq.com/access-control.html#combined-backends)

It is possible to use multiple backends for authn or authz using the auth_backends configuration key

 When several authentication backends are used then <u>the first positive result</u> returned by a backend in the chain is considered to be final.   This should not be confused with mixed backends (for example, using LDAP for authentication and internal backend for authorisation). 

```ini
auth_backends.1 = internal
#internal for rabbit_auth_backend_internal
#ldap for rabbit_auth_backend_ldap (from the LDAP plugin)
#http for rabbit_auth_backend_http (from the HTTP auth backend plugin)
#amqp for rabbit_auth_backend_amqp (from the AMQP 0-9-1 auth backend plugin)
#dummy for rabbit_auth_backend_dummy
```

The following example configures RabbitMQ to use the [LDAP backend](https://www.rabbitmq.com/ldap.html) for both authentication and authorisation. *Internal database will not be consulted:*

```ini
auth_backends.1 = ldap
```

This will check LDAP first, and then fall back to the http database if the user cannot be authenticated through LDAP:

```ini
auth_backends.1 = ldap
# uses module name instead of a short alias, "http"
auth_backends.2 = rabbit_auth_backend_http

# See HTTP backend docs for details
auth_http.user_path = http://my-authenticator-app/auth/user
auth_http.vhost_path = http://my-authenticator-app/auth/vhost
auth_http.resource_path = http://my-authenticator-app/auth/resource
auth_http.topic_path = http://my-authenticator-app/auth/topic
```



The example below is fairly advanced. It will check LDAP first. If the user is found in LDAP then the password will be checked against LDAP and subsequent authorisation checks will be performed against the internal database (therefore users in LDAP must exist in the internal database as well, but do not need a password there). If the user is not found in LDAP then a second attempt is made using only the internal database:

```ini
# rabbitmq.conf
#
auth_backends.1.authn = ldap
auth_backends.1.authz = internal
auth_backends.2       = internal
```

## [Authentication Mechanisms](https://www.rabbitmq.com/access-control.html#mechanisms)

RabbitMQ supports multiple SASL authentication mechanisms. There are three such mechanisms built into the server: `PLAIN`, `AMQPLAIN`, and `RABBIT-CR-DEMO`, and one — `EXTERNAL` — available as a [plugin](https://github.com/rabbitmq/rabbitmq-auth-mechanism-ssl).

### [Built-in Authentication Mechanisms](https://www.rabbitmq.com/access-control.html#available-mechanisms)

The built-in mechanisms are:

| Mechanism      | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| PLAIN          | SASL PLAIN authentication. This is enabled by default in the RabbitMQ server and clients, and is the default for most other clients. |
| AMQPLAIN       | Non-standard version of PLAIN retained for backwards compatibility. This is enabled by default in the RabbitMQ server. |
| EXTERNAL       | Authentication happens using an out-of-band mechanism such as [x509 certificate peer verification](https://github.com/rabbitmq/rabbitmq-auth-mechanism-ssl), client IP address range, or similar. Such mechanisms are usually provided by RabbitMQ plugins. |
| RABBIT-CR-DEMO | Non-standard mechanism which demonstrates challenge-response authentication. This mechanism has security equivalent to PLAIN, and is **not** enabled by default in the RabbitMQ server. |

### [Mechanism Configuration in the Server](https://www.rabbitmq.com/access-control.html#server-mechanism-configuration)

The configuration variable `auth_mechanisms` in the rabbit application determines which of the installed mechanisms are offered to connecting clients. This variable should be a list of atoms corresponding to mechanism names, for example `['PLAIN', 'AMQPLAIN']` by default. The server-side list is not considered to be in any particular order. See the [configuration file](https://www.rabbitmq.com/configure.html#configuration-file) documentation.

#### [Mechanism Configuration in Java](https://www.rabbitmq.com/access-control.html#client-mechanism-configuration-java)

The Java client does not use the javax.security.sasl package by default since this can be unpredictable on non-Oracle JDKs and is missing entirely on Android. There is a RabbitMQ-specific SASL implementation, configured by the SaslConfiginterface. A class DefaultSaslConfig is provided to make SASL configuration more convenient in the common case. A class JDKSaslConfig is provided to act as a bridge to javax.security.sasl.

ConnectionFactory.getSaslConfig() and ConnectionFactory.setSaslConfig(SaslConfig) are the primary methods for interacting with authentication mechanisms.

## [Troubleshooting Authentication](https://www.rabbitmq.com/access-control.html#troubleshooting-authn)

[Server logs](https://www.rabbitmq.com/logging.html) will contain entries about failed authentication attempts:

```ini
2019-03-25 12:28:19.047 [info] <0.1613.0> accepting AMQP connection <0.1613.0> (127.0.0.1:63839 -> 127.0.0.1:5672)
2019-03-25 12:28:19.056 [error] <0.1613.0> Error on AMQP connection <0.1613.0> (127.0.0.1:63839 -> 127.0.0.1:5672, state: starting):
PLAIN login refused: user 'user2' - invalid credentials
2019-03-25 12:28:22.057 [info] <0.1613.0> closing AMQP connection <0.1613.0> (127.0.0.1:63839 -> 127.0.0.1:5672)
```

[rabbitmqctl authenticate_user](https://www.rabbitmq.com/cli.html) can be used to test authentication for a username and password pair:

```bash
rabbitmqctl authenticate_user 'a-username' 'a/password'
```

![1571810349440](RabbitMq%20Access%20Control.assets/1571810349440.png)

`rabbitmqctl authenticate_user` will use an CLI-to-node communication connection to attempt to authenticate the username/password pair against an internal API endpoint. The connection is assumed to be trusted. If that's not the case, its traffic can be [encrypted using TLS](https://www.rabbitmq.com/clustering-ssl.html).

> Per AMQP 0-9-1 spec, authentication failures should result in the server closing TCP connection immediately. However, with RabbitMQ clients can opt in to receive a more specific notification using the [authentication failure notification](https://www.rabbitmq.com/auth-notification.html)extension to AMQP 0-9-1. Modern client libraries support that extension transparently to the user: no configuration would be necessary and authentication failures will result in a visible returned error, exception or other way of communicating a problem used in a particular programming language or environment.

[rabbitmqctl list_permissions](https://www.rabbitmq.com/cli.html) can be used to inspect a user's permission in a given virtual host:

```bash
rabbitmqctl list_permissions --vhost /
# => Listing permissions for vhost "/" ...
# => user    configure   write   read
# => user2   .*  .*  .*
# => guest   .*  .*  .*
# => temp-user   .*  .*  .*
```

 [Server logs](https://www.rabbitmq.com/logging.html) will contain entries about operation authorisation failures. For example, if a user does not have any permissions configured for a virtual host: 