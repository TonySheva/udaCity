docker是环境容器。

它有一个dockerhub，类似与github的“生态圈”

它可以commit， push， pull 类似于git 

常用命令：

如果需要push我们的docker images

docker images 首先查看存在的images

我们需要标记（tag）images 的名字。

docker tag name 192.168.2.124:5000/develop 

docker commit 

docker push 192.168.2.124:5000/develop

另一台机器需要这个docker images 

docker pull 192.168.2.124:5000/develop

使用docker 

首先需要运行docker images

docker run -it --rm -P --privileged --name test3 
-v ~/project/src:/datasystem/src -v ~/vpn:/vpn 
-p 3000:3000 -p 6379:6379 -p 5433:5432 
192.168.2.124:5000/develop
详细查看--help 
常见点：将主机目录对应docker里的目录，端口号，名称，以及tag name

你可以通过docker ps -a 
可以查看所有的docker 的container 
如果查看正在运行的，就直接docker ps 即可。

开启docker：
docker start test3 

docker attach test3

现在进去入了docker

如果需要多个终端同时进入同一个docker 

docker exec -i -t `sudo docker ps|grep test3|awk '{print $1}'` bash

