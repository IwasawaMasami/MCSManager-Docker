## Docker部署 MCSManager面板控制




#### 前言

长话短说一共有两个版本dockerfile~~（其实就一个）~~

\~~~~因为 git clone下载太慢了，所以直接在本地复制过去构建了~~~~

两个方法：

1.直接部署（简单化）

2.构建后部署（定制化）



**所以觉得慢的同学可以选择本地的那个进行部署或构建**

#### 部署:

####  第一种：直接使用docker命令部署（省事）：

```shell
docker run -it -d --name=mcsm  -v ~/自定义宿主机目录:/root/mcsmanager/你想要挂载的容器目录 tookizhang/mcsmanager
```
**镜像里默认开放了默认游戏服务端端口，面板访问端以及和文件传输端口，如果需要批量开放端口请加入参数 -p XXXXX-XXXXX:XXXXX-XXXXX 
eg:
```shell
docker run -it -d --name mcsm -p 25565:25565 -p 23333:23333 -p 25566-25570:25566-25570 -v ~/mcsmamager:/root/mcsmanager/server mcsmanager
```




docker images 【查看已有镜像】

docker ps 【查看容器进程】

docker logs -f 容器名字 【查看容器内的日志】

docker exec -it 容器名字 bash 【进入consle界面】

docker start 容器名字 【开始运行容器】

docker stop 【停止正在运行容器】

docker restart 【重启容器】



每个参数的含义：

-d 就是把这个容器挂起来，让它能够持续运行

-v 通过映射的方式将物理主机的目录对应到容器里的目录

-p 端口映射，因为容器是独立隔离的，所以需要把容器端口映射到主机端口上去

--name 这个就是为你的容器起一个名字



**PS :如果想要定制dockerfile的同学可以选择第二种：**

####  第二种：使用dockerfile进行build构建然后在运行（略麻烦）

##### 1.复制这段代码然后保存为文件命名为dockerfile（大小写无所谓）

```
FROM centos:7
LABEL maintainer="www.lazytoki.cn"
RUN yum -y install wget
RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
    && yum makecache \
           update
RUN yum -y install screen \
    git \
    curl \
    java
RUN curl --silent --location https://rpm.nodesource.com/setup_10.x | bash - \
    && yum install -y nodejs
RUN git clone https://github.com/suwings/mcsmanager.git
RUN cd mcsmanager \
    && npm install
WORKDIR /root/mcsmanager
RUN cd /root/mcsmanager \
    && npm install
WORKDIR /root/mcsmanager
ENTRYPOINT npm start
EXPOSE 23333 10022 25565
```

##### 2.在当前目录下使用命令
```shell
docker build -t  mcsmanager .
```

**嫌git clone慢的同学可以使用下面的dockerfile,但是必须得先把双羽大佬的源码给克隆到当前目录才可以：**

```
FROM centos:7
LABEL maintainer="www.lazytoki.cn"
RUN yum -y install wget
RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
    && yum makecache \
           update
RUN yum -y install screen \
    git \
    curl \
    java
RUN curl --silent --location https://rpm.nodesource.com/setup_10.x | bash - \
    && yum install -y nodejs
COPY mcsmanager /root/mcsmanager 
WORKDIR /root/mcsmanager
RUN cd /root/mcsmanager \
    && npm install
WORKDIR /root/mcsmanager
ENTRYPOINT npm start
EXPOSE 23333 10022 25565
```

**最后构建完成后，如第一种方法所示运行镜像即可**



#### 结语：

**对的 你没看错 我写了 很多层的RUN（可能这就是真的菜吧哈哈哈哈**
