> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [www.jianshu.com](https://www.jianshu.com/p/4fc53d7d7620)

#### 一、安装

1、执行安装命令

```
brew install mysql
```

2、安装完后启动 mysql

```
mysql.server start
```

3、执行安全设置

```
mysql_secure_installation
```

显示如下

```
There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file
```

按照提示选择密码等级，并设置 root 密码

#### 二、创建新的数据库、用户并授权

1、登录 mysql

```
mysql -u root -p
```

按提示输入 root 密码

```
root@poksi-test-2019:~# mysql -u root -p
Enter password:
```

2、创建数据库

```
create database retail_db character set utf8mb4;
```

3、创建用户

```
create user 'retail_u'@'%' identified by 'retail_PWD_123';
```

4、授权用户

```
grant all privileges on retail_db.* to 'retail_u'@'%';
```

```
flush privileges;
```

5、查看当前的数据库

```
show databases;
```

6、显示当前数据库的表单

```
show tables
```

#### 三、建表

```
CREATE TABLE t_user(
  key_id VARCHAR(255) NOT NULL PRIMARY KEY,  -- id 统一命名为key_id
  user_name VARCHAR(255) NOT NULL ,
  password VARCHAR(255) NOT NULL ,
  phone VARCHAR(255) NOT NULL,
  deleted INT NOT NULL DEFAULT 0,  -- 逻辑删除标志默认值
  create_time   timestamp NULL default CURRENT_TIMESTAMP, -- 创建时间默认值
  update_time   timestamp NULL default CURRENT_TIMESTAMP -- 修改时间默认值
) ENGINE=INNODB AUTO_INCREMENT=1000 DEFAULT CHARSET=utf8mb4;
```

#### 四、检查 mysql 服务状态

先退出 mysql 命令行，输入命令

```
systemctl status mysql.service
```

显示如下结果说明 mysql 服务是正常的

```
● mysql.service - MySQL Community Server
  Loaded: loaded (/lib/systemd/system/mysql.service; enabled; vendor preset: enabled)
  Active: active (running) since Wed 2019-05-22 10:53:13 CST; 13min ago
Main PID: 16686 (mysqld)
   Tasks: 29 (limit: 4667)
  CGroup: /system.slice/mysql.service
          └─16686 /usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid

May 22 10:53:12 poksi-test-2019 systemd[1]: Starting MySQL Community Server...
May 22 10:53:13 poksi-test-2019 systemd[1]: Started MySQL Community Server.
```



## 修改用户

update user set authentication_string=password("root") where user="root"and host="localhost";



UPDATE mysql.user set authentication_string = password ("root") WHERE User = "root" and Host = "localhost";

mysqladmin -u root -h localhost -p password "root"



alter user user() identified by “root”;
set global validate_password.policy=0;
set global validate_password.length=4;
set global validate_password.check_user_name=OFF;
set global validate_password.number_count=0;
set global validate_password.special_char_count=0;
flush privileges;
ALTER USER 'root'@localhost IDENTIFIED WITH mysql_native_password BY 'root' ;
update user set host='localhost' where user ='root';

