## nginx

```bash
# 拉取官方镜像 - 面向docker的只读模板
docker pull nginx
# 查看
docker images nginx
# 启动镜像
mkdir www
echo 'hello docker!!' >> www/index.html

# 启动
# www目录里面放一个index.html
# 第一次需要在ecs安全组策略开放对应的端口 8000，否则无法访问。
docker run -p 8000:80 -v $PWD/www:/usr/share/nginx/html nginx

# -d 后台形式启动，启动完成会返回一个containerid
docker run -p 8000:80 -v $PWD/www:/usr/share/nginx/html -d nginx

# 后台启动
docker run -p 80:80 -v $PWD/www:/usr/share/nginx/html -d nginx

# 停止进程，只需打containerid的前三位
docker stop ff6

# 启动
docker start ff6


# 查看进程，只查看正在运行的
docker ps

# 查看全部，包括停止的
docker ps -a 

# 伪终端 ff6容器的uuid
docker exec -it ff6 /bin/bash

# 退出伪终端
exit

# 删除镜像
docker rm ff6

```

## rabbitMq

```bash
# for RabbitMQ 3.9, the latest series
docker run -it --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.9-management
```



## 镜像（Image）

面向Docker的只读模板

## 容器（Container）

镜像的运行实例

## 仓库 (Registry)

存储镜像的服务器



## 定制image

- 创建Dockerfile文件

```BASH
#Dockerfile
FROM nginx:latest
RUN echo '<h1>Hello, Kaikeba!</h1>' > /usr/share/nginx/html/index.html
```

- build

```BASH
# 定制镜像
# -t 镜像名:版本 .表示dockerfile在当前目录下
docker build -t nginx:kaikeba .
# 运行
docker run -p 80:80 -d nginx:kaikeba
```



### 定制node

```dockerfile
#Dockerfile
#制定node镜像的版本 该镜像体积很小,已经内置npm
FROM node:10-alpine
#移动当前目录下面的文件到app目录下
ADD . /app/
#进入到app目录下面，类似cd
WORKDIR /app
#安装依赖
RUN npm install
#对外暴露的端口
EXPOSE 3000
#程序启动脚本
CMD ["node", "app.js"]
```



### Pm2 - 利用多核资源



```bash
# .dockerignore
node_modules
```

- process.yml 描述运行

```yml
// process.yml
apps:
- script : app.js
instances: 2
watch : true
env :
NODE_ENV: production
```

- Dockerfile

```dockerfile
# Dockerfile
FROM keymetrics/pm2:latest-alpine
WORKDIR /usr/src/app
ADD . /usr/src/app
RUN npm config set registry https://registry.npm.taobao.org/ && \
npm i
EXPOSE 3000
#pm2在docker中使用命令为pm2-docker
CMD ["pm2-runtime", "start", "process.yml"]	
```



```bash
# 定制镜像
docker build -t mypm2 .
# 运行
docker run -p 3000:3000 -d mypm2
```



### Compose

简介
Compose项目是 Docker 官方的开源项目，负责实现对 Docker 容器集群的快速编排。

```bash
apt install docker-compose
```



```yml
# for test
version: '3.1'
services:
  hello-world:
    image: hello-world
```



```yml
#docker-compose.yml
version: '3.1'
services:
  mongo:
    image: mongo
    restart: always
    ports:
      - 27017:27017
  mongo-express:
    image: mongo-express
    restart: always
    ports:
      - 8000:8081

```



-  启动

```bash
docker-compose up
```



### Deploy



- 安装vscode插件：Deploy

https://github.com/su37josephxia/docker_ci

- deploy的配置文件，写在.vscode/setting.json

```bash
{
    "deploy": {
        "packages": [{
            "files": [
                "**/*",
            ],

            "exclude": [
                "node_modules/**",
                ".git/**",
                ".vscode/**",
                "**/node_modules/**",
            ],
            "deployOnSave": false
        }],
        "targets": [{
            "type": "sftp",
            "name": "AliyunServer",
            "dir": "/root/source/docker_ci",
            "host": "8.134.115.230",
            "port": 22,
            "user": "root",
            "privateKey": "/Users/Administrator/.ssh/id_rsa"
        }],
    },
}
```



