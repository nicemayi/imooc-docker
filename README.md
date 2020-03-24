# imooc-docker

1. dockerhub nicemayi ZWzw198496

2. namespaces, tcontrols groups, union file systems

3. 157.230.169.141, 157.230.166.146, 165.22.185.94

4. vagrant up

5. vagrant ssh

6. linux kernel bootfs -> linux distribution -> image

7. image read only

8. docker image ls

9. 
```
FROM -> base image
LABEL -> author
RUN apt-get update && apt-get install something
EXPOSE 6379
ENTRYPOINT ["some cmd"]
```

10. docker pull ubuntu:14.04

11. <username>/<imagename>:<versionnumber>

12. versionnumber默认latest

13. sudo groupadd docker && sudo gpasswd -a <username> docker && sudo service docker restart, 重新登录就可以不再需要加sudo了

14. docker pull hello-world

15. docker run hello-world

16. docker build -t nicemayi/hello-world .

17. FROM scratch is a no-op in the Dockerfile

18. 记住！！！ -t加tag是给image用的。--name是给container用的！

19. env GOOS=linux GOARCH=amd64 go build

20. docker run nicemayi/hwgo

21. image layers (RO) -> container layer (RW)

22. docker container ls -a

23. CMD是运行时执行的命令

24. docker run centos

25. docker run -it centos

26. docker container rm <id> 简写 docker rm <id>

27. docker container ls 简写 docker ps

28. docker image ls 简写docker images

29. docker image rm 简写 docker rmi

30. docker container ls -aq 列出所有container id

31. docker rm $(docker container ls -aq) 删除所有的containers

32. 删除所有退出的容器: `docker rm $(docker container ls -f "status=exited" -q)`

33. docker container commit -> docker commit

34. docker image build -> docker build

35. docker commit <container name> <username>/<image-name>:<tag>

36. docker build -t tag .

37. 可以有多条LABEL

38. RUN用\换行，每一个RUN就是一层，尽量一层

39. WORKDIR设定container当前工作目录，会自己创建

40. 不要用RUN cd，尽量使用绝对目录

41. ADD可以添加并且解压，COPY只能COPY,大部分COPY优于ADD

42. ENV设定环境变量 `ENV MYSQL_VERSION 5.6`

43. docker login, docker push <your image>

44. 本地起一个私有的docker registry
`docker run -d -p 5000:50000 --restart always --name registry`

45. /etc/doceker/daemon.json -> { "insecure-registries": ["ip:port"] }
  
46. sudo more /lib/systemd/system/docker.service -> ENvironmentFile=-/etc/docker/daemon.json

47. docker build -t <ip:port>/<imagename>

48. docker exec -it <number> python

49. docker contaner stop -> doceker stop

50. `docker run --name=demo -p 5000:5000 -v /Users/zwang/Documents/imooc-docker/flaskdocker/flask-demo:/app flaskdemo`

51. 容器资源限制 --memory=200M --vm 1 --verbose --vm-bytes=500M --cpu-shares

55. Docker network
单机
Bridge Network
Host Network
None Network
多机
Overlay Network

56. wireshark抓包工具

57. network namespace

58. ip netns list; ip netns add test1

59. ip netns exec test1 ip link set dev lo up

60. Docker bridge0

61. docker network ls

62. docker network inspect <network id>

63. 容器互联就是分别通过veth pair都连接到docker0网桥上

64. 容器连接外网：网桥通过NAT（网络地址转换，通过iptables）连接到eth0

65. docker run -d --name test2 --link test1 busybox /bin/sh -c "while true; do sleep 3600; done"
然后在test2中可以直接ping test1，或者比如数据库mysql test1

66. 注意link是有方向的，其实用处不大，使用docker compose

67. docker可以创建bridge

68. docker network create -d bridge my-bridge

69. docker run -d --name test3 --network my-bridge busybox /bin/sh -c "while true; do sleep 3600; done"

70. docker network connect my-bridge test1 #可以连接一个container to network （有记忆性的，相当于改了container的设置）

71. 端口转发 <宿主port>:<容器port>

72. none网络表示谁都不能访问的网络（用处未知）

73. host网络就表示完全使用主机的网络（表示容器没有自己的network namespace），可能端口有冲突 --network host

74. docker run --name flaskredisdemo0 -p 5000:5000 --link redis -e REDIS_HOST=redis -v /Users/zwang/Documents/imooc-docker/flaskdocker/flask-demo/:/app flaskredisdemo

docker run \
    --name flaskredisdemo0 \
    --port 5000:5000 \
    --link redis \
    --env REDIS_HOST=redis \
    --volume /Users/zwang/Documents/imooc-docker/flaskdocker/flask-demo/:/app \
    flaskredisdemo

75. docker run --help

76. node1 157.230.169.141, node2 157.230.166.146

77. VXLAN, underlay & overlay

78. 分布式存储etcd

79.
node1
nohup ./etcd --name docker-node1 --initial-advertise-peer-urls http://157.230.169.141:2380 \
--listen-peer-urls http://157.230.169.141:2380 \
--listen-client-urls http://157.230.169.141:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://157.230.169.141:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://157.230.169.141:2380,docker-node2=http://157.230.166.146:2380 \
--initial-cluster-state new&

node2
nohup ./etcd --name docker-node2 --initial-advertise-peer-urls http://157.230.166.146:2380 \
--listen-peer-urls http://157.230.166.146:2380 \
--listen-client-urls http://157.230.166.146:2379,http://127.0.0.1:2379 \
--advertise-client-urls http://157.230.166.146:2379 \
--initial-cluster-token etcd-cluster \
--initial-cluster docker-node1=http://157.230.169.141:2380,docker-node2=http://157.230.166.146:2380 \
--initial-cluster-state new&

./etcdctl cluster-health

80. 需要手动指定docker集群

在docker-node1上

```
$ sudo service docker stop
$ sudo /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock --cluster-store=etcd://192.168.205.10:2379 --cluster-advertise=192.168.205.10:2375&
```

即启动docker服务的时候指定etcd的cluster即可，所以之后在本地的所有的操作，就会通过overlay同步到云上的另一台机器上

81. docker volume是利用docker自己创建的一个目录

82. docker run -d -v mysql:/var/lib/mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true --name mysqldemo1 mysql

83. COPY是一次性的拷贝到image里面，而bind mount是实时的sym link到image中

84. docker compose: services, networks, volumes (都是复数) version 3可以用在多机，version 2只能用在单机

85. docker-compose -f docker-compose.yml up -d

86. docker-compose -f ./coding-189/chapter6/labs/wordpress/docker-compose.yml ps

87. docker-compose -f ./coding-189/chapter6/labs/wordpress/docker-compose.yml images

88. docker-compose -f ./coding-189/chapter6/labs/wordpress/docker-compose.yml exec mysql bash

89. image/context, docerfile

90. docker-compose up --scale web=3 -d

91. docker swarm
```
swarm manager (node1): 157.230.169.141
swarm worker1 (node2): 157.230.166.146
swarm worker2 (node3): 165.22.185.94
```
92. 
On node1:
```
docker swarm init --advertise-addr=157.230.169.141
```
Then on node1 should see:
```
zhe@node1:~$ docker swarm init --advertise-addr=157.230.169.141
Swarm initialized: current node (hwfpxo1fs4nk4u7kx5fv8gl1x) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-3keqoemrs23v15wmsfp6kx8kjjmadtqzcc9liij6u8mga3sxaw-7ftnvcd4xzls5m9k6j0bkvqrw 157.230.169.141:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Then on node2/node3, paste the cmd ^ from node1, should see 
```
zhe@node2:~$ docker swarm join --token SWMTKN-1-3keqoemrs23v15wmsfp6kx8kjjmadtqzcc9liij6u8mga3sxaw-7ftnvcd4xzls5m9k6j0bkvqrw 157.230.169.141:2377
This node joined a swarm as a worker.

zhe@node3:~$ docker swarm join --token SWMTKN-1-3keqoemrs23v15wmsfp6kx8kjjmadtqzcc9liij6u8mga3sxaw-7ftnvcd4xzls5m9k6j0bkvqrw 157.230.169.141:2377
This node joined a swarm as a worker.
```

Then on node1, check swarm cluster status:
```
zhe@node1:~$ docker node ls
ID                            HOSTNAME            STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
hwfpxo1fs4nk4u7kx5fv8gl1x *   node1               Ready               Active              Leader              18.09.6
0m1ia6o9v9g4gai9cjxmjn8ca     node2               Ready               Active                                  19.03.2
p9t0edgv019816mhzrejwbu5h     node3               Ready               Active                                  19.03.2
```

To leave swarm cluster, just run `docker swarm leave`

93. 在swarm集群之上就可以用`docker service create`来创建服务了

94. 在swarm manager上运行`docker service create --name demo busybox sh -c "while true; do sleep 3600; done"`, should see
```
zhe@node1:~$ docker service create --name demo busybox sh -c "while true; do sleep 3600; done"
2i503jaxgkjwj7iw9453nnu6b
overall progress: 1 out of 1 tasks
1/1: running   [==================================================>]
verify: Service converged

zhe@node1:~$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
2i503jaxgkjw        demo                replicated          1/1                 busybox:latest

zhe@node1:~$ docker service ps demo
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
0t73zls9lvs7        demo.1              busybox:latest      node1               Running             Running about a minute ago

zhe@node1:~$ docker ps
CONTAINER ID        IMAGE                  COMMAND                  CREATED             STATUS              PORTS               NAMES
f56c58c274ce        busybox:latest         "sh -c 'while true; …"   2 minutes ago       Up 2 minutes                            demo.1.0t73zls9lvs7x1hos62s7f0lw
```

95. 水平扩展服务: `docker service scale demo=5`

```
zhe@node1:~$ docker service scale demo=5
demo scaled to 5
overall progress: 5 out of 5 tasks
1/5: running   [==================================================>]
2/5: running   [==================================================>]
3/5: running   [==================================================>]
4/5: running   [==================================================>]
5/5: running   [==================================================>]
verify: Service converged

zhe@node1:~$ docker service ls
ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
2i503jaxgkjw        demo                replicated          5/5                 busybox:latest

zhe@node1:~$ docker service ps demo
ID                  NAME                IMAGE               NODE                DESIRED STATE       CURRENT STATE            ERROR               PORTS
0t73zls9lvs7        demo.1              busybox:latest      node1               Running             Running 4 minutes ago
xgle072anlyt        demo.2              busybox:latest      node3               Running             Running 48 seconds ago
srs5su8mwhft        demo.3              busybox:latest      node1               Running             Running 51 seconds ago
fjnhfq6l2no1        demo.4              busybox:latest      node2               Running             Running 49 seconds ago
982dtwsqevxw        demo.5              busybox:latest      node3               Running             Running 47 seconds ago
```

96. 删除服务: `docker service rm demo`

```
zhe@node1:~$ docker service ps demo
no such service: demo
```

97. docker network create -d overlay demo 创建一个网络

98. MySQL service
```
docker service create \
    --name mysql \
    --env MYSQL_ROOT_PASSWORD=root \
    --env MYSQL_DATABASE=wordpress \
    --network demo \
    --mount type=volume,source=mysql-data,destination=/var/lib/mysql \
    mysql:5.7

# 注意mysql必须是5.7的

docker service ps mysql
```

99. WordPress service
```
docker service create \
    --name wordpress \
    -p 80:80 \
    --env WORDPRESS_DB_PASSWORD=root \
    --env WORDPRESS_DB_HOST=mysql \
    --network demo \
    wordpress
```
输入谁的地址都可以！！！

100. docker swarm内置DNS服务发现

101. Routing Mesh -> Internal, Ingress (指的是即使某个服务只在某个节点上，访问集群也能访问到这个服务！！！)\

102. DNS + VIP + iptables + LVS (负载均衡)

103. docker stack, version 3增加了deploy命令，可以deploy docker-compose.yml

104.
```
docker stack deploy wordpress --compose-file=docker-compose.yml
```

```
zhe@node1:~$ docker stack ps wordpress
ID                  NAME                                        IMAGE               NODE                DESIRED STATE       CURRENT STATE                ERROR               PORTS
qk1u82ltozq9        wordpress_mysql.hwfpxo1fs4nk4u7kx5fv8gl1x   mysql:5.7           node1               Running             Running 2 minutes ago
lwmijejyrh3f        wordpress_web.1                             wordpress:latest    node3               Running             Running about a minute ago
dxods61aomad        wordpress_web.2                             wordpress:latest    node1               Running             Running about a minute ago
qyciukyou6y8        wordpress_web.3                             wordpress:latest    node2               Running             Running about a minute ago
```

105. 查看log `docker service logs --tail=all --since=0 wordpress_web`

106. docker stack rm wordpress 停止服务

107. doceker secret management

108. 在service中只要使用 --secret <my-pw>就可以使用了 (在container的/run/secrets/my-pw文字中会有明文)

109. 在compose文件中
```
  mysql:
    image: mysql
    secrets:
      - my-pw # 事先创建好的

或者
    secrets:
        my-pw:
            file: ./password
```

110. service更新
```
docker service update --image <newimage> web # 滚动更新
```