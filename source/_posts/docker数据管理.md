---
title: docker数据管理
date: 2026-05-10 21:57:14
tags: docker
categories: docker
series: docker
---

## 一、背景
生产环境中使用Docker的过程中，往往需要对数据进行持久化，或者需要在多个容器之间进行数据共享，这必然涉及容器的数据管理操作。
容器中管理数据主要有两种方式：
* 数据卷（Data Volumes）：容器内数据直接映射到本地主机环境；
* 数据卷容器（Data Volume Containers）：使用特定容器维护数据卷。

## 二、数据卷
数据卷是一个可供容器使用的特殊目录，它将主机操作系统目录直接映射进容器，类似于Linux中的mount操作。

数据卷可以提供很多有用的特性，如下所示：
* 数据卷可以在容器之间共享和重用，容器间传递数据将变得高效方便；
* 对数据卷内数据的修改会立马生效，无论是容器内操作还是本地操作；
* 对数据卷的更新不会影响镜像，解耦了应用和数据；
* 卷会一直存在，直到没有容器使用，可以安全地卸载它。

### 2.1 创建数据卷
在用docker run命令的时候，使用-v标记可以在容器内创建一个数据卷。多次重复使用-v标记可以创建多个数据卷。
下面使用training/webapp镜像创建一个web容器，并创建一个数据卷挂载到容器的/webapp目录：
```shell
$ docker run -d -P --name web -v /webapp training/webapp python app.py
```

使用-v标记也可以指定挂载一个本地的已有目录到容器中去作为数据卷（推荐方式）。
```shell
$ docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py
```
> 本地目录的路径必须是绝对路径，如果目录不存在Docker，会自动创建。

## 三、数据卷容器

如果用户需要在多个容器之间共享一些持续更新的数据，最简单的方式是使用数据卷容器。数据卷容器也是一个容器，但是它的目的是专门用来提供数据卷供其他容器挂载。
首先，创建一个数据卷容器dbdata，并在其中创建一个数据卷挂载到/dbdata：
```shell
$ docker run -it -v /dbdata --name dbdata ubuntu
```

然后，可以在其他容器中使用--volumes-from来挂载dbdata容器中的数据卷，例如创建db1和db2两个容器，并从dbdata容器挂载数据卷：
```shell
$ docker run -it --volumes-from dbdata --name db1 ubuntu
$ docker run -it --volumes-from dbdata --name db2 ubuntu
```

此时，容器db1和db2都挂载同一个数据卷到相同的/dbdata目录。三个容器任何一方在该目录下的写入，其他容器都可以看到。
>可以多次使用--volumes-from参数来从多个容器挂载多个数据卷。还可以从其他已经挂载了容器卷的容器来挂载数据卷。

## 四、利用数据卷来备份恢复数据
### 4.1 备份
```shell
$ docker run --volumes-from dbdata -v $(pwd):/backup --name worker ubuntu tar cvf /backup/backup.tar /dbdata
```

* 首先利用ubuntu镜像创建了一个容器worker。
* 使用--volumes-from dbdata参数来让worker容器挂载dbdata容器的数据卷（即dbdata数据卷）；
* 使用-v$(pwd):/backup参数来挂载本地的当前目录到worker容器的/backup目录
* worker容器启动后，使用了tar cvf/backup/backup.tar/dbdata命令来将/dbdata下内容备份为容器内的/backup/backup.tar，即宿主主机当前目录下的backup.tar。