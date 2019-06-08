---
title: dockerfile 最佳实践
date: 2019-06-08 23:57:40
tags: devops docker
---
![](http://pssf2j165.bkt.clouddn.com/hIF_K6PRjko.jpg)
# DockerFile 最佳实践

## 概述
	
Docker通过读取DockerFile里面的内容自动build镜像。DockerFile是一个包含了build过程中需要执行的所有命令文件。

## 基本方针与建议

### 容器应该要精简
	
镜像生成的容器越精简越好：容器可以很容易的stop和destroy，也很容易创建新的容器，通过较少的配置就可以部署使用。

### 使用 .dockerignore文件

想要快速的进行upload，或者提高build的效率。那么建议使用.dockerignore文件。

### 避免安装不必要的包

减少build复杂度，软件依赖度，镜像大小和build时间。

### 一个容器只运行一个实例

在大部分情况下，尽量一个容器下只运行一个实例，将耦合的应用分别装到不同的容器，便于扩展和复用。如果容器之间存在依赖性，建议使用container linking。

### 最小化镜像层数

要在DockerFile可读性和减少镜像层数量间取得平衡。

### 分割多行参数

尽可能将安装的软件包安装顺序排列。避免重复安装。使用'\'进行分割，增强代码可读性。
```dockerfile
	RUN apt-get update && apt-get install -y \
	bzr\
	cvs\
	git\
	mercurial\
	subversion
```

### build 缓存

Docker在build 镜像的时候，会按照Dockerfile中规定的步骤依次执行。Docker执行每一条命令的时候都会去判断有没有存在的镜像层或者可以复用的镜像层。如果你不想使用cache中的数据，那么执行docker build时添加 --no-cache=true。

cache规则：
	* 如果缓存中存在base image，那么递归检查dockerfile中所有的数据层定义是否和缓存中的base image数据层定义相同。如果不同，缓存数据无效。
	*  在大多数情况下，将dockerfile中的image数据层对比就足够了。
	*   针对ADD COPY 这些命令，docker会检查这些文件。每次计算文件的checksum。如果checksum不匹配，缓存中的文件就会失效。
	*   除了ADD COPY回去比对数据，其他命令不会比对，只会比较执行的命令字符串是否相同。

一旦缓存中的数据失效了，那么该命令后的之后的所有命令都不会使用缓存的数据，而是生成新的数据层。


