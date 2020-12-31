```sh

$ docker version #查看版本
# 或
$ docker run hello-world

$ docker run -it ubuntu bash
    # cat /etc/os-release

$ docker container ls  #或 docker ps -a //只有一个Ubuntu

$ docker info

$ git clone https://github.com/dwyl/learn-docker.git

$ docker build -t learn-docker .
# 这个命令只能进入看看
$ docker run -it -p 8888:8888 learn-docker bash 
# 加上 -d 后台运行
$ docker run -it -d -p 8888:8888 learn-docker
# 本地访问
$ http://localhost:8888/
    # Hello Docker!
# 进入运行中的容器
$ docker exec -it learn-docker bash

$ docker images

$ docker ps -a

$ docker inspect {imageid}

# 获取IP
$ docker inspect --format '{{ .NetworkSettings.IPAddress }}' container_name_or_id
    # 172.17.0.2
# 或
$ docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id

$ docker stop {container_id}

# 其他
$ sudo nginx -t
    # nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    # nginx: configuration file /etc/nginx/nginx.conf test is successful

```

- Docker and "PETE" stack
```sh
FROM elixir:1.7.3

RUN mix local.hex --force \
  && mix archive.install hex phx_new 1.4.0 --force \
  && apt-get update \
  && curl -sL https://deb.nodesource.com/setup_8.x | bash \
  && apt-get install -y apt-utils \
  && apt-get install -y nodejs \
  && apt-get install -y build-essential \
  && apt-get install -y inotify-tools \
  && mix local.rebar --force \
  && wget "https://github.com/elm/compiler/releases/download/0.19.0/binaries-for-linux.tar.gz" \
  && tar xzf binaries-for-linux.tar.gz \
  && mv elm /usr/local/bin/

ENV APP_HOME /app
WORKDIR $APP_HOME


CMD ["mix", "phx.server"]
```

```sh
# docker-compose.yml
version: '3'
services:
  app:
    build: .
    ports:
      - "4000:4000"
    volumes:
      - .:/app
    depends_on:
      - db
    env_file:
      - ./.env
  db:
    image: postgres:10.5
    ports:
        - "5432:5432"


$ ocker-compose run --rm app mix phx.new . --app name_of_the_app
$ ocker-compose run --rm app mix phx.new . --app name_of_the_app

```


- Docker Swarm vs. Kubernetes
    - 介绍
    - https://docs.docker.com/engine/swarm/
    - http://kubernetes.io/

- Useful Links
    - 参见原链接





## 参考链接

- https://github.com/dwyl/learn-docker