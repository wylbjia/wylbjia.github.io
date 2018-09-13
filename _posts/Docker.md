## docker 常用命令

```
Build Ship Run
镜像 仓库 容器 
yum -y install docker	     
systemctl start docker
docker search ubuntu             //寻找镜像
docker pull [OPTIONS] NAME [:TAG]
docker pull ubuntu    //下载镜像，默认下载latest镜像
docker images     //查看docker下载的镜像
docker images ubuntu    //查看ubuntu镜像
docker run -it ubuntu   // -t 在新容器内指定一个伪终端或终端，-i 允许交互（使用exit，或者ctrl+D退出） -d 后台启动
docker ps         //查看正在运行的docker进程
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
docker logs ID    //查看容器内的标准输出
docker stop ID    //停止容器、
docker rmi ID     //删除镜像
docker rm         //删除容器
docker cp         //在host和container之间拷贝文件
docker commit  原镜像ID 新镜像ID   //保存改动为新的image
docker build -t ubuntu-with-vi-dockerfile .  //使用dockerfile新建docker镜像
docker history ubuntu-wit-vi-dockerfile    //查看镜像的构建历史，以及分层信息
docker tag       //设置标签
```

> ##  docker file

```
FROM  Base image
RUN   执行命令
COPY   src dest | ["src","dest"] 
ADD    和COPY类似，如果是归档文件，文件会自动被解压到dest
CMD    执行命令
EXPOSE 暴露端口
WORKDIR 指定工作路径
MAINTAINER 维护者
ENV 	环境变量
ENTRYPOINT	容器入口
USER	指定用户
VOLUME	mount point
```

