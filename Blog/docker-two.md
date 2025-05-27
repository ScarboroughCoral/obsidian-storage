---
title: 多容器APP之Compose学习笔记(二)
date: 2018-03-07 13:07:14

tags:
- Docker
- Docker-Compose
categories:
- Tools
- Docker
---


{% note default %}
人类善用工具，是因为懒。
{% endnote %}

<!-- more -->

### 前言

已经不能称之为学习笔记了，因为官方文档实在是枯燥，于是我就直接对着某个实例上了。本次我搭建了一个可以复用的环境，`Mysql+Tomcat+Nginx`可能这样搭配有点不合理，或许，毕竟对这方面了解的不多，不过搭建出来确实能用。而且能够复用，你只需要将你的JavaWeb工程打包成War文件放到Tomcat的webapps目录下就行了。

### Compose服务配置 

这个环境中需要三层服务，一是数据库层，二是app层，三是nginx用作代理层。因为是比较小的项目所以没有做反向代理的配置，但是此环境可用于扩展，将Tomcat部署到不同的模拟服务器下，并用nginx反向代理。这是可扩展的。下面是我的目录结构：

```
env_template
│   
│   docker-compose.yml
│
└───lostfound
│   │   
│   │   Dockerfile
│   
└───nginx
    │   
    │   nginx.conf
    │   Dockerfile
```

服务配置使用默认名为docker-compose.yml的配置文件来配置的。下面是我的配置文件
```yaml
version: '2'
networks:
  lostfound:
services:
  lostfound:
    build: lostfound
    networks:
      - lostfound
    volumes:
      - $PWD/lostfound/webapps:/usr/local/tomcat/webapps
    depends_on:
      - db
    ports:
      - "8080:8080"
  nginx:
    build: nginx
    networks:
      - lostfound
    depends_on:
      - lostfound
    ports:
      - "80:80"
  db:
    image: "mysql:5.7.15"
    networks:
      - lostfound 
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_USER: lostfound
      MYSQL_PASSWORD: ******
    volumes:
      - $PWD/data:/var/lib/mysql
    ports:
      - "3306:3306"

```

简单说一下，三层服务配置。三项服务在同一个网络`lostfound`内，lostfound层就是app层，是从工程目录下的lostfound文件夹构建的，里面就是tomcat，端口映射8080。为了容易部署我的web应用程序，我将lostfound层的`tomcat/webapps`挂载到本地。然后app层依赖于数据库层，等待数据库层构建运行，数据库层应用了Mysql5.7里面有一些环境的配置，另外为了使得数据持久化，我将容器中的保存数据的文件夹也挂载到本地，端口映射为3306，**如果你的3306端口被占用，时会报错的，你需要停止你的3306端口的应用程序，一般来说是Mysql服务**。使用`sudo service mysql stop`来结束本地的mysql服务。然后是nginx服务，这层服务对于这种小工程来说不是必需的，但是为了可扩展我就加上了。端口映射为默认端口80。

### Tomcat 构建

这个比较简单，就是`pull`一个镜像，并暴露端口8080。这是其Dockerfile文件：

```Dockerfile
FROM tomcat
EXPOSE 8080
```

### Nginx 构建

这个也是`pull`一个镜像，然后将代理设置一下。

- Dockerfile
```Dockerfile
FROM nginx
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
```
- nginx.conf
```nginx
worker_processes 4;
events {worker_connections 1024;}
http {
    server {
        listen 80;
        location / {
            proxy_pass http://lostfound:8080;
        }
    }
}
```

> 将tomcat运行的webapp代理到nginx。


### 其他

> 运行此环境需要你安装以下工具：
- Docker
- Docker-Compose

### 运行

```bash
docker-compose up
```
> 打开浏览器，地址栏内输入`localhost/yourappname`

### Github地址

> https://github.com/ScarboroughCoral/SE_Docker

