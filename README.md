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