---
title: docker容器的使用
date: 2026-05-07 21:51:48
tags: docker
categories: docker
series: docker系列
---


## 一、创建容器
### 1.1 容器是镜像的一个实例

```shell
docker create -it ubuntu:latest

docker ps -a
```

使用docker create 创建的容器处于停止状态，可以使用 docker start 启动它

### 1.2  启动容器
```shell
docker start af
```

### 1.3 新建并启动容器
docker run 等价于 docker create + docker start
**Example: 输出后容器自动终止**
```shell
docker run ubuntu /bin/echo 'Hello Word'
```

**docker run 之后，后台操作**
1. 检查本地是否存在指定的镜像，不存在就从公有仓库下载；
2. 利用镜像创建一个容器，并启动该容器；
3. 分配一个文件系统给容器，并在只读的镜像层外面挂载一层可读写层；
4. 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中；
5. 从网桥的地址池配置一个IP地址给容器；
6. 执行用户指定的应用程序；
7. 执行完毕后容器被自动终止。


### 1.4 终止容器
可以使用docker stop来终止一个运行中的容器
>docker kill命令会直接发送SIGKILL信号来强行终止容器. 此外，当Docker容器中指定的应用终结时，容器也会自动终止

### 1.5 进入容器
* attach
```shell
docker attach
```

* exec 可以直接在容器内直接执行任意命令
```shell
$ docker exec -it 243c32535da7  /bin/bash
root@243c32535da7:/#
```
> **通过指定-it参数来保持标准输入打开，并且分配一个伪终端。通过exec命令对容器执行操作是最为推荐的方式。**

## 二、删除容器
docker rm命令只能删除处于终止或退出状态的容器，并不能删除还处于运行状态的容器.
如果要直接删除一个运行中的容器，可以添加-f参数, Docker会先发送SIGKILL信号给容器，终止其中的应用，之后强行删除

## 三、导入导出容器

### 3.1 导出
```shell
$ docker export -o test_for_run.tar ce5
$ ls
test_for_run.tar
$ docker export e81 >test_for_stop.tar
$ ls
test_for_run.tar test_for_stop.tar

```

### 3.2 导入
```shell
$ docker import test_for_run.tar - test/ubuntu:v1.0
$ docker images
REPOSITORY        TAG      IMAGE ID       CREATED              VIRTUAL SIZE
test/ubuntu       v1.0     9d37a6082e97   About a minute ago   171.3 MB

```

