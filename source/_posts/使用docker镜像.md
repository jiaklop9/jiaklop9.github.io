---
title: 使用docker镜像
date: 2026-05-07 20:49:27
tags: docker
categories: docker
series: docker系列
---


## 一、概览
1. 如何使用pull命令从Docker Hub仓库中下载镜像到本地
2. 如何查看本地已有的镜像信息和管理镜像标签
3. 如何在远端仓库使用search命令进行搜索和过滤
4. 如何删除镜像标签和镜像文件
5. 如何创建用户定制的镜像并且保存为外部文件
6. 如何往Docker Hub仓库中推送自己的镜像

## 二、获取镜像

```shell
docker pull name[:Tag]
```
> name 后不带标签默认最新 == name:latest

example:
```shell
14.04: Pulling from library/ubuntu
6c953ac5d795: Pull complete
3eed5ff20a90: Pull complete
f8419ea7c1b5: Pull complete
51900bc9e720: Pull complete
a3ed95caeb02: Pull complete
Digest: sha256:97421885f3da3b23f52eeddcaa9f8f91172a8ac3cd5d3cd40b51c7aad09f66cc
Status: Downloaded newer image for ubuntu:14.04
```

下载过程中可以看出，镜像文件一般由若干层（layer）组成，6c953ac5d795这样的串是层的唯一id（实际上完整的id包括256比特，由64个十六进制字符组成）。使用docker pull命令下载时会获取并输出镜像的各层信息。当不同的镜像包括相同的层时，本地仅存储层的一份内容，减小了需要的存储空间

## 三、查看镜像

```shell
docker images
```

#### 3.1 添加标签
为了方便在后续工作中使用特定镜像，还可以使用docker tag命令来为本地镜像任意添加新的标签。例如添加一个新的myubuntu：latest镜像标签：
```shell
$ docker tag ubuntu:latest myubuntu:latest
```

#### 3.2 使用inspect命令查看详细信息

使用inspect命令查看详细信息
```shell
docker inspect ubuntu:14.04
```
#### 3.3 使用history命令查看镜像历史
既然镜像文件由多个层组成，那么怎么知道各个层的内容具体是什么呢？这时候可以使用history子命令，该命令将列出各层的创建信息。
```shell    
docker history ubuntu:14.04

IMAGE         CREATED      CREATED BY                                     SIZE COMMENT
8f1bd21bd25c  2 weeks ago  /bin/sh -c #(nop) CMD ["/bin/bash"]            0 B
<missing>     2 weeks ago  /bin/sh -c sed -i 's/^#\s*\(deb.*universe\)$/  1.895 kB
<missing>     2 weeks ago  /bin/sh -c rm -rf /var/lib/apt/lists/*         0 B
<missing>     2 weeks ago  /bin/sh -c set -xe   && echo '#!/bin/sh' > /u  194.5 kB
<missing>     2 weeks ago  /bin/sh -c #(nop) ADD file:aca501360d0937bc49  187.8 MB

```

## 四、搜寻、删除镜像
```shell
docker search
```

使用docker rmi命令可以删除镜像，命令格式为docker rmi IMAGE[IMAGE...]，其中IMAGE可以为标签或ID。
#### 4.1 标签删除
当同一个镜像拥有多个标签的时候，docker rmi命令只是删除该镜像多个标签中的指定标签而已，并不影响镜像文件
但当镜像只剩下一个标签的时候就要小心了，此时再使用docker rmi命令会彻底删除镜像
#### 4.2 镜像ID删除
当使用docker rmi命令，并且后面跟上镜像的ID（也可以是能进行区分的部分ID串前缀）时，会先尝试删除所有指向该镜像的标签，然后删除该镜像文件本身。
>当有该镜像创建的容器存在时，镜像文件默认是无法被删除的

## 五、创建镜像

创建镜像的方法主要有三种：
* 基于已有镜像的容器创建、
* 基于本地模板导入、
* 基于Dockerfile创建。

#### 5.1 基于已有镜像创建容器


该方法主要是使用docker commit命令。命令格式为
```shell 
docker commit[OPTIONS]CONTAINER[REPOSITORY[：TAG]]
```
主要选项包括：
```shell
·-a，--author=""：作者信息；
·-c，--change=[]：提交的时候执行Dockerfile指令，包括CMD|ENTRYPOINT|ENV|EXPOSE|LABEL|ONBUILD|USER|VOLUME|WORKDIR等；
·-m，--message=""：提交消息；
·-p，--pause=true：提交时暂停容器运行。
```
**Example**
下面将演示如何使用该命令创建一个新镜像。首先，启动一个镜像，并在其中进行修改操作，例如创建一个test文件，之后退出：
```shell
$ docker run -it ubuntu:14.04 /bin/bash
root@a925cb40b3f0:/# touch test
root@a925cb40b3f0:/# exit
```
记住容器的ID为a925cb40b3f0。
此时该容器跟原ubuntu：14.04镜像相比，已经发生了改变，可以使用docker commit命令来提交为一个新的镜像。提交时可以使用ID或名称来指定容器：
```shell
$ docker commit -m "Added a new file" -a "Docker Newbee" a925cb40b3f0 test:0.1
9e9c814023bcffc3e67e892a235afe61b02f66a947d2747f724bd317dda02f27
```
顺利的话，会返回新创建的镜像的ID信息，例如9e9c814023bcffc3e67e892a235afe61b02f66a947d2747f724bd317dda02f27。
此时查看本地镜像列表，会发现新创建的镜像已经存在了：
```shell
$ docker images
REPOSITORY        TAG     IMAGE ID        CREATED           VIRTUAL SIZE
test              0.1     9e9c814023bc    4 seconds ago     188 MB
```

#### 5.2 本地导入

用户也可以直接从一个操作系统模板文件导入一个镜像，主要使用docker import命令。命令格式为
```shell
docker import[OPTIONS]file|URL|-[REPOSITORY[:TAG]]。
```

## 六、存出和载入镜像

```shell
$ docker images
REPOSITORY          TAG         IMAGE ID        CREATED         VIRTUAL SIZE
ubuntu              14.04       8f1bd21bd25c    2 weeks ago     188 MB
...
$ docker save -o ubuntu_14.04.tar ubuntu:14.04
```

```shell
$ docker load --input ubuntu_14.04.tar

or 

$ docker load < ubuntu_14.04.tar
```


### 七、EXAMPLE

```shell
#设置继承镜像
FROM ubuntu:14.04
#提供一些作者的信息
MAINTAINER docker_user (user@docker.com)
#下面开始运行更新命令
RUN apt-get update
#安装ssh服务
RUN apt-get install -y openssh-server
RUN mkdir -p /var/run/sshd
RUN mkdir -p /root/.ssh
#取消pam限制
RUN sed -ri 's/session    required     pam_loginuid.so/#session    required     
    pam_loginuid.so/g' /etc/pam.d/sshd
#复制配置文件到相应位置,并赋予脚本可执行权限
ADD authorized_keys /root/.ssh/authorized_keys
ADD run.sh /run.sh
RUN chmod 755 /run.sh
#开放端口
EXPOSE 22
#设置自启动命令
CMD ["/run.sh"]
```