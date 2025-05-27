---
title: 多容器APP之Compose学习笔记(一)
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

这一段时间比较乱,不知道要干点什么,索性就直接刷了几本书.现在在刷的是《Java Web 应用开发与案例教程》，这本书本人觉着比较棒，虽然讲了JSP的使用，毕竟现在JSP不是主流了，应该是如此，自从前后端分离之后，HTML，CSS，JavaScript的出现，使客户端实现动态网页流行了起来。但其中的一些知识点还是需要知道的，比如说ServletContext、Session、Request、PageContext的生命周期，转发和重定向等。恩，接下来进入主题。

### Compose的学习

前几天对Docker的入门直接刷了一个Mooc，但是不怎么系统。就像那样说的`现在手机使得知识点变得零碎起来，而系统学习是必须的`。那如何做到系统学习？一是书籍，而是官方教程。我认为如此。我打算在[官方教程](https://docs.docker.com/compose/)里面看一下Compose的系统学习思路。

### Compose的构建过程

首先要明确一点，我们用Docker是来干嘛的？用Compose是用来干嘛的？Docker是为了让工程摆脱环境，Compose是集成多个容器，这是因为有时候单个容器并不能完成我们的需求。那么Compose是如何构建的呢？

我们思考，为什么Docker让工程摆脱了环境？这是因为Docker已经将环境也顺便打包到集装箱里了。那么构建过程第一步便是底层环境的构建，`任何应用必须运行在操作系统上`，这里就是Dockerfile的构建，当然这里面可能有一系列命令，总之Compose会让Dockerfile里面的命令先执行完毕。就像下面这个文件一样
```Dockerfile
FROM python:3.4-alpine
ADD . /code
WORKDIR /code
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```
然后第二步便会找Compose的配置文件`docker-compose.yml`这里面配置了你所需要的服务，需要哪些容器镜像，配置端口等等。
```yml
version: '3'
services:
  web:
    build: .
    ports:
     - "5000:5000"
  redis:
    image: "redis:alpine"
```

这是构建过程
```bash
Building web
Step 1/5 : FROM python:3.4-alpine
3.4-alpine: Pulling from library/python
81033e7c1d6a: Pull complete
9b61101706a6: Pull complete
415e2a07c89b: Pull complete
f22df7a3f000: Pull complete
8c16bf19c1f9: Pull complete
Digest: sha256:fe436cb066394d81cf49448a04dec7c765082445a500bc44f1ae5e8a455793bd
Status: Downloaded newer image for python:3.4-alpine
 ---> 5c72717ec319
Step 2/5 : ADD . /code
 ---> 73b857a9e7be
Step 3/5 : WORKDIR /code
Removing intermediate container 36713f6d9d18
 ---> 7de417856227
Step 4/5 : RUN pip install -r requirements.txt
 ---> Running in f9b8a9c9be05
Collecting flask (from -r requirements.txt (line 1))
  Downloading Flask-0.12.2-py2.py3-none-any.whl (83kB)
Collecting redis (from -r requirements.txt (line 2))
  Downloading redis-2.10.6-py2.py3-none-any.whl (64kB)
Collecting Jinja2>=2.4 (from flask->-r requirements.txt (line 1))
  Downloading Jinja2-2.10-py2.py3-none-any.whl (126kB)
Collecting itsdangerous>=0.21 (from flask->-r requirements.txt (line 1))
  Downloading itsdangerous-0.24.tar.gz (46kB)
Collecting click>=2.0 (from flask->-r requirements.txt (line 1))
  Downloading click-6.7-py2.py3-none-any.whl (71kB)
Collecting Werkzeug>=0.7 (from flask->-r requirements.txt (line 1))
  Downloading Werkzeug-0.14.1-py2.py3-none-any.whl (322kB)
Collecting MarkupSafe>=0.23 (from Jinja2>=2.4->flask->-r requirements.txt (line 1))
  Downloading MarkupSafe-1.0.tar.gz
Building wheels for collected packages: itsdangerous, MarkupSafe
  Running setup.py bdist_wheel for itsdangerous: started
  Running setup.py bdist_wheel for itsdangerous: finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/fc/a8/66/24d655233c757e178d45dea2de22a04c6d92766abfb741129a
  Running setup.py bdist_wheel for MarkupSafe: started
  Running setup.py bdist_wheel for MarkupSafe: finished with status 'done'
  Stored in directory: /root/.cache/pip/wheels/88/a7/30/e39a54a87bcbe25308fa3ca64e8ddc75d9b3e5afa21ee32d57
Successfully built itsdangerous MarkupSafe
Installing collected packages: MarkupSafe, Jinja2, itsdangerous, click, Werkzeug, flask, redis
Successfully installed Jinja2-2.10 MarkupSafe-1.0 Werkzeug-0.14.1 click-6.7 flask-0.12.2 itsdangerous-0.24 redis-2.10.6
Removing intermediate container f9b8a9c9be05
 ---> 9daf3f18ec3b
Step 5/5 : CMD ["python","app.py"]
 ---> Running in bd6fd9143af8
Removing intermediate container bd6fd9143af8
 ---> 159831743522
Successfully built 159831743522
Successfully tagged composetest_web:latest
WARNING: Image for service web was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Pulling redis (redis:alpine)...
alpine: Pulling from library/redis
ff3a5c916c92: Already exists
aae70a2e6027: Pull complete
87c655da471c: Pull complete
a0bd51ac7350: Pull complete
755565c3ea2b: Pull complete
8bf100ea488d: Pull complete
Digest: sha256:4f54ade2260d5c80283d044ab9657f390cb8effd9a7a847765693a3670f6874a
Status: Downloaded newer image for redis:alpine
Creating composetest_web_1   ... done
Creating composetest_redis_1 ... done
Attaching to composetest_web_1, composetest_redis_1

```