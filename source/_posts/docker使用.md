---
title: docker使用
date: 2022-01-19 19:39:22
categories:
tags:
---

首先在本机上安装 docker，参照

https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

需要将本用户加入 docker 用户组或者一直使用 sudo

```
# 添加docker用户组，一般已存在，不需要执行

sudo groupadd docker

# 将登陆用户加入到docker用户组中

sudo gpasswd -a $USER docker

# 更新用户组

newgrp docker

# 测试docker命令是否可以使用sudo正常使用

docker version
```

然后 check 是否安装成功，使用 `docker run hello-world` 一般来说第一次会 pull

然后就进入我猜配置阶段啦，可以先看看这个 https://zhuanlan.zhihu.com/p/166672337



如果要使用 mysql 最好单独开一个 docker

https://www.jianshu.com/p/d9b6bbc7fd77

参考一下这个

http://www.1os.top/it/cloud/docker/docker-django/

哦还有这一篇手把手教你的

https://segmentfault.com/u/dusai

基本上配置了一个 django，可以打开的网页，然后先尝试单独打包

当然要先 migrate  `python manage.py migrate` 然后写入 Dockerfile

```
# 从仓库拉取 带有 python 3.7 的 Linux 环境
FROM python:3.7

# 设置 python 环境变量
ENV PYTHONUNBUFFERED 1

# 创建 code 文件夹并将其设置为工作目录
RUN mkdir /code
WORKDIR /code
# 更新 pip
RUN pip install pip -U
# 将 requirements.txt 复制到容器的 code 目录
COPY requirements.txt /code/
# 安装库
RUN pip install -r requirements.txt
# 将当前目录复制到容器的 code 目录
ADD . /code/
```

哎呀官方推荐使用 copy

当然了这个是 用了 py 3.9

`pipenv lock -r > requirements.txt`

生成 requirements.txt

然后使用 docker-compose 毕竟以后要启用多个，写入**docker-compose.yml**

```
version: "3"
services:
  app:
    restart: always
    build: .  # '点'代表当前目录
    command: "python3 manage.py runserver 0.0.0.0:8000"
    volumes:
      - .:/code
    ports:
      - "8000:8000"
```

restart， 在出现意外的时候定时重启。 - .:/code 左边是本机，右边是 docker，两个连接到一起，类似于虚拟机的共享文件夹？

`volumes` ：**卷，这是个很重要的概念。**前面说过容器是和宿主机完全隔离的，但是有些时候又需要将其连通；比如我们开发的 Django 项目代码常常会更新，并且更新时还依赖如 Git 之类的程序，在容器里操作就显得不太方便。所以就有**卷**，它定义了宿主机和容器之间的映射：**"."** 表示宿主机的当前目录，**":"** 为分隔符，"/code" 表示容器中的目录。即宿主机当前目录和容器的 /code 目录是连通的，宿主机当前目录的 Django 代码更新时，容器中的 /code 目录中的代码也相应的更新了。这有点儿像是在容器上打了一个洞，某种程度上也是**实用性**和**隔离性**的一种妥协。



遇到了端口占用，记得把 command 和 ports 的端口都修改

在 setting 里面把 0.0.0.0 给加到 ALLOWED_HOSTS = ['.localhost', '127.0.0.1', '0.0.0.0'] 似乎是 127 不能监视什么的不管了。



然后添加 mysql，应该来说似乎 mysql 8 有什么特殊的登录方式所以要使用别的参数



先在 yaml 那里加入 mysql，然后更改 dockerfiles

草，看来要尝试 postgres 了，然后说是不 empty 所以需要先删一个

https://stackoverflow.com/questions/62697071/docker-compose-postgres-upgrade-initdb-error-directory-var-lib-postgresql-da

```
docker volume ls
docker volume inspect db_data <-- will show you the mountpoint

```



pipenv 无法直接安装 psycopg2,要安装 psycopg2-binary，然后输出 requirements

有时候可以在 run 后面加上 -y 来搞定 apt 安装的交互



##### frontend react 安装

嗯好像每次 npm 总得出事……

https://stackoverflow.com/questions/46013544/yarn-install-command-error-no-such-file-or-directory-install

如果安装了就先 remove 掉

```
sudo apt remove cmdtest
sudo apt remove yarn
```

Install it simple via npm

```
npm install -g yarn
```

记得退出原来的 pipenv 环境！然后

```
yarn install
yarn run
```

然后报错……

`/bin/sh: 1: react-scripts: not found`

查看了一下需要先运行一下 yarn，直接`yarn`一下之后得到了是 node 版本太低

卧槽，node 这么麻烦……这家伙要手动切换版本……算了一般麻烦吧，使用 nvm 切换版本

`nvm install [version]`

`nvm use [version]`

然后再 install, follow above steps

```
npm install -g yarn
yarn
yarn install
yarn start
```

这样就可以了



dockerfile 设置环境，docker-compose 定义服务

所以简单来说这次的 dockerfile 就是跑 yarn

https://codefresh.io/docs/docs/learn-by-example/nodejs/react/

找到一个 example

```
FROM node
RUN mkdir /frontend
WORKDIR /frontend

COPY package.json yarn.lock ./
RUN yarn
COPY . /frontend/
RUN yarn
RUN yarn build
```

整个是这样的

```
version: "3.1"
services:
  db:
    image: postgres
    ports:
      - "5432:5432"
    restart: always
    environment:
      - POSTGRES_NAME=postgres
      - POSTGRES_PASSWORD=example
  frontend:
    restart: always
    ports:
      - "3000:3000"
    build: ./frontend
    command: bash -c "yarn start"
    volumes:
      - ./frontend:/frontend
  backend:
    restart: always
    build: ./backend  # dockerfile position
    command: bash -c "python3 manage.py migrate && python3 manage.py runserver 0.0.0.0:8010"
    volumes:
      - ./backend:/backend
    ports:
      - "8010:8010"
    environment:
      - DB_ENGINE=django.db.backends.postgresql
      - DB_NAME=postgres
      - DB_USER=postgres
      - DB_PASSWORD=example
      - DB_HOST=db
      - DB_PORT=5432
    depends_on:
      - db
```

我也不指望自己能够学会，就下次再来看吧