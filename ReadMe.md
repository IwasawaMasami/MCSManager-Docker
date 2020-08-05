Docker部署 MCSManager面板控制
最近几日闲的无聊，于是就想写写dockerfile练练手，于是就折腾上了双羽大佬的MC面板控制，之前也看过用docker部署的面板，但是貌似都不是一步到位的，所以就趁着闲的这阵子折腾折腾，在下非码农出身就想学点东西，望各位大佬们多多包含（磕头）。

前言
长话短说一共有两个版本dockerfile~~（其实就一个）~~
~~因为 git clone下载太慢了，所以直接在本地复制过去构建了~23333~
两个方法：
1.直接部署（简单化）
2.构建后部署（定制化）

所以觉得慢的同学可以选择本地的那个进行部署或构建

部署
第一种：直接使用docker命令部署（省事）：

docker run -it -d --name=mcsm -p 23333:23333 -v ~/mcsm:/root/mcsmanager tookizhang/mcsmanager:gitonline
docker run -it -d --name=mcsm -p 23333:23333 -v ~/mcsm:/root/mcsmanager tookizhang/mcsmanager:gitlocal

docker images 【查看已有镜像】
docker ps 【查看容器进程】
docker logs -f 容器名字 【查看容器内的日志】
docker exec -i 容器名字 rcon-cli 【进入服务端consle界面】
docker start 容器名字 【开始运行容器】
docker stop 【停止正在运行容器】
docker restart 【重启容器】

每个参数的含义：
-d 就是把这个容器挂起来，让它能够持续运行
-v 通过映射的方式将物理主机的目录对应到容器里的目录
-p 端口映射，因为容器是独立隔离的，所以需要把容器端口映射到主机端口上去
--name 这个就是为你的容器起一个名字
如果想要定制dockerfile的同学可以选择第二种：

第二种：使用dockerfile进行build构建然后在运行（略麻烦）

1.复制这段代码然后保存为文件命名为dockerfile（大小写无所谓）

📋
LABEL maintainer="www.lazytoki.cn"
RUN yum -y install wget 
RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
    && yum makecache \
           update
RUN yum -y install screen \
    git \
    curl 
RUN curl --silent --location https://rpm.nodesource.com/setup_10.x | bash - \
    && yum install -y nodejs 	
RUN git clone https://github.com/suwings/mcsmanager.git 
RUN cd mcsmanager \
    && npm install 
WORKDIR /mcsmanager	
CMD npm start
EXPOSE 23333 10022
2.在当前目录下使用命令**docker build -t mcsmanager .

嫌git clone慢的同学可以使用下面的dockerfile,但是必须得先把双羽大佬的源码给克隆到当前目录才可以：

📋
LABEL maintainer="www.lazytoki.cn"
RUN yum -y install wget 
RUN wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
    && yum makecache \
           update
RUN yum -y install screen \
    git \
    curl 
RUN curl --silent --location https://rpm.nodesource.com/setup_10.x | bash - \
    && yum install -y nodejs 	
COPY mcsmanager /root/mcsmanager
RUN cd /root/mcsmanager \
    && npm install 
WORKDIR /root/mcsmanager
CMD npm start
EXPOSE 23333 10022

最后构建完成后，如第一种方法所示运行镜像即可

结语：

对的 你没看错 我写了 很多层的RUN（可能这就是菜吧哈哈哈哈
