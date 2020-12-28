## 1
```sh
    $ docker run -i -t ubuntu /bin/bash
      | - docker pull ubuntu
      | - docker container create
```

## 2. paly-with-docker
### 面向IT专业人员和系统管理员的Docker第1阶段
> https://training.play-with-docker.com/
- 您的第一个Linux容器
- 自定义Docker images
- 部署和管理多个容器

```sh
    uname -a
    docker container start <container ID>

    docker container run hello-world

    docker image pull alpine
    docker image ls

    docker container run alpine ls -l

    docker container run alpine echo "hello from alpine"

    docker container run -it alpine /bin/sh

    docker container ls -a

    docker container run --help

    docker container start <container ID>

    docker container exec <container ID> ls

    --------
    docker image inspect alpine
    docker image pull
    docker container run hello-world
    docker run
    docker container ls
```

```sh
    docker container run -ti ubuntu bash
        apt-get update
        apt-get install -y figlet
        figlet "hello docker"

        exit
    
    docker container ls -a
    docker container diff <container ID>
    docker container commit CONTAINER_ID

    docker image ls

    docker image tag <IMAGE_ID> ourfiglet
    docker image ls

    docker container run ourfiglet figlet hello

    index.js
        var os = require("os");
        var hostname = os.hostname();
        console.log("hello from " + hostname);
    
    Dockerfile
        FROM alpine
        RUN apk update && apk add nodejs
        COPY . /app
        WORKDIR /app
        CMD ["node","index.js"]

    docker image build -t hello:v0.1 .

    docker container run hello:v0.1

    docker image history <image ID>

    echo "console.log(\"this is v0.2\");" >> index.js

    docker image build -t hello:v0.2 .

    ---------------
    docker image pull alpine
    docker image inspect alpine
    docker image inspect --format "{{ json .RootFS.Layers }}" alpine

    docker image inspect --format "{{ json .RootFS.Layers }}" <image ID>

```

```sh
    Docker Compose | Docker Swarm Mode

    192.168.0.12
        //初始化  - 经理
        docker swarm init --advertise-addr $(hostname -i)
            Swarm initialized: current node (o3y0git6ns7mak2yutia62xxs) is now a manager.

            To add a worker to this swarm, run the following command:

            //用来将worker节点连接到swarm的命令
            docker swarm join --token SWMTKN-1-49t5s9jsz72k9p4shvv3820fo1k3dj7do22j5x3fpbyfq90lfb-5kpfjm10xaz22zo8dkymdqk7j 192.168.0.7:2377

            //用于添加其他管理器
            To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

        //应该输出两个节点
        docker node ls

        git clone https://github.com/docker/example-voting-app
        cd example-voting-app

        cat docker-stack.yml

        docker stack deploy --compose-file=docker-stack.yml voting_stack

        docker stack ls

        docker stack services voting_stack
        //目前只有2个vote
        docker service ps voting_stack_vote

        //5个vote
        docker service scale voting_stack_vote=5



    192.168.0.13
        //工人
        docker swarm join --token SWMTKN-1-49t5s9jsz72k9p4shvv3820fo1k3dj7do22j5x3fpbyfq90lfb-5kpfjm10xaz22zo8dkymdqk7j 192.168.0.7:2377


```
### 面向IT专业人员和系统管理员的Docker第二阶段

- 了解Docker的体系结构和核心功能
- 了解如何将Docker集成到您现有的应用程序基础架构中
- 开发概念验证应用程序部署

#### 安全

#### 网络
```sh
    docker network

    docker network ls

    docker network inspect bridge

    docker info
    -------------
    docker network ls

    //安装brctl命令并使用它列出Docker主机上的Linux桥。您可以通过运行来做到这一点sudo apt-get install bridge-utils

    apk update
    apk add bridge

    brctl show

    ip a
    
    //步骤2 连接容器
    docker run -dt ubuntu sleep infinity

    docker ps

    brctl show

    docker network inspect bridge

    //步骤3 测试网络连接
    docker ps

    docker exec -it <CONTAINER ID> /bin/bash

    apt-get update && apt-get install -y iputils-ping

    ping -c5 www.github.com

    exit

    docker stop <CONTAINER ID>

    //步骤4 为外部连接配置NAT
    docker run --name web1 -d -p 8080:80 nginx

    docker ps

    curl 127.0.0.1:8080

    --------------------
    第三节 覆盖网络
    //步骤1 基础知识
    docker swarm init --advertise-addr $(hostname -i)

    复制docke swarm join ……到第二个终端

    //在a中
    docker node ls

    //步骤2 创建覆盖网络
    docker network create -d overlay overnet

    docker network ls

    注：创建Swarm集群时，会自动创建其他新网络（ingress和docker_gwbridge）

    //b
    docker network ls //没有overnet

    //a
    docker network inspect overnet

    //步骤3 建立服务
    //a
    docker service create --name myservice \
--network overnet \
--replicas 2 \
ubuntu sleep infinity

    docker service ls

    docker service ps myservice

    //b
    docker network ls
    docker network inspect overnet

    //步骤4 测试网络
    docker network inspect overnet

    docker ps

    docker exec -it <CONTAINER ID> /bin/bash

    该服务的IP地址10.0.0.3来自该docker network inspect overnet命令。

    apt-get update && apt-get install -y iputils-ping

    root@d676496d18f7:/# ping -c5 10.0.0.3

    //步骤5 测试服务发现
    ocker exec -it <CONTAINER ID> /bin/bash

    cat /etc/resolv.conf

    root@d676496d18f7:/# ping -c5 myservice

    exit

    docker service inspect myservice


    //打扫干净
    docker service rm myservice

    docker ps

    docker kill yourcontainerid1 yourcontainerid2

    //node 1
    docker swarm leave --force
    //node 2
    docker swarm leave --force

```

#### 编排
- Docker Orchestration动手实验室
- https://training.play-with-docker.com/ops-stage2/


```sh
    3台服务器
    //node 1
    docker run -dt ubuntu sleep infinity

    docker ps

    docker swarm init --advertise-addr $(hostname -i)

    docker info

    //node 2
    docker swarm join \
    --token SWMTKN-1-1wxyoueqgpcrc4xk2t3ec7n1poy75g4kowmwz64p7ulqx611ih-68pazn0mj8p4p4lnuf4ctp8xy \
    10.0.0.5:2377

    //node 3
    docker swarm join \
    --token SWMTKN-1-1wxyoueqgpcrc4xk2t3ec7n1poy75g4kowmwz64p7ulqx611ih-68pazn0mj8p4p4lnuf4ctp8xy \
    10.0.0.5:2377

    //node 1
    docker node ls

    docker service create --name sleep-app ubuntu sleep infinity

    docker service ls

    docker service update --replicas 7 sleep-app

    docker service ps sleep-app

    docker service update --replicas 4 sleep-app  //把数量减少到4个

    docker service ps sleep-app
        ID            NAME         IMAGE          NODE     DESIRED STATE  CURRENT STATE           ERROR  PORTS
        7k0flfh2wpt1  sleep-app.1  ubuntu:latest  node1  Running        Running 13 minutes ago
        wol6bzq7xf0v  sleep-app.2  ubuntu:latest  node3  Running        Running 5 minutes ago
        35t0eamu0rue  sleep-app.6  ubuntu:latest  node2  Running        Running 5 minutes ago
        44s8d59vr4a8  sleep-app.7  ubuntu:latest  node1  Running        Running 5 minutes ago

    docker node ls
        ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
        6dlewb50pj2y66q4zi3egnwbi *  node1   Ready   Active        Leader
        ym6sdzrcm08s6ohqmjx9mk3dv    node3   Ready   Active
        yu3hbegvwsdpy9esh9t2lr431    node2   Ready   Active

    //node 2
    docker ps

    //node 1
    docker node ls   //获取一下node 2的ID

    docker node update --availability drain yournodeid

    docker node ls
        ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
        6dlewb50pj2y66q4zi3egnwbi *  node1   Ready   Active        Leader
        ym6sdzrcm08s6ohqmjx9mk3dv    node3   Ready   Active
        yu3hbegvwsdpy9esh9t2lr431    node2   Ready   Drain

    //node 2
    docker ps   //显示空

    //node 1
    docker service ps sleep-app
        ID            NAME             IMAGE          NODE     DESIRED STATE  CURRENT STATE           ERROR  PORTS
        7k0flfh2wpt1  sleep-app.1      ubuntu:latest  node1  Running        Running 25 minutes ago
        wol6bzq7xf0v  sleep-app.2      ubuntu:latest  node3  Running        Running 18 minutes ago
        s3548wki7rlk  sleep-app.6      ubuntu:latest  node3  Running        Running 3 minutes ago
        35t0eamu0rue   \_ sleep-app.6  ubuntu:latest  node2  Shutdown       Shutdown 3 minutes ago
        44s8d59vr4a8  sleep-app.7      ubuntu:latest  node1  Running        Running 18 minutes ago

    打扫干净
    //node 1
    docker service rm sleep-app

    docker ps
        //ubuntu
    docker kill id //删除ubuntu

    //node 1 node 2 node 3
    docker swarm leave --force

```


## docker-stack.yml
```sh
version: "3"
services:

  redis:
    image: redis:alpine
    networks:
      - frontend
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure
  db:
    image: postgres:9.4
    environment:
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
    deploy:
      placement:
        constraints: [node.role == manager]
  vote:
    image: dockersamples/examplevotingapp_vote:before
    ports:
      - 5000:80
    networks:
      - frontend
    depends_on:
      - redis
    deploy:
      replicas: 2
      update_config:
        parallelism: 2
      restart_policy:
        condition: on-failure
  result:
    image: dockersamples/examplevotingapp_result:before
    ports:
      - 5001:80
    networks:
      - backend
    depends_on:
      - db
    deploy:
      replicas: 1
      update_config:
        parallelism: 2
        delay: 10s
      restart_policy:
        condition: on-failure

  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    depends_on:
      - db
      - redis
    deploy:
      mode: replicated
      replicas: 1
      labels: [APP=VOTING]
      restart_policy:
        condition: on-failure
        delay: 10s
        max_attempts: 3
        window: 120s
      placement:
        constraints: [node.role == manager]

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - "8080:8080"
    stop_grace_period: 1m30s
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    deploy:
      placement:
        constraints: [node.role == manager]

networks:
  frontend:
  backend:

volumes:
  db-data:
```

## 参考链接

- [Docker overview
](https://docs.docker.com/get-started/overview/)
