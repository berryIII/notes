作为一个java/scala/sqler日常工作虽不会涉及的docker，但作为一个打杂专业户还是偶尔要用到，这里贴出常用命令方便作为使用时的速查手册。

## 安装
安装docker，如果不想折腾homebrew或是apt这类包管理工具，推荐直接安装docker的desktop版本，安装好后在命令行或是通过gui均可使用，[官方下载链接](https://www.docker.com/get-started/)。
安装好后执行经典version验证安装成功。`docker --version`

## 常用命令
### 启动运行
```bash
#运行镜像
docker run mirrors.tencent.com/todacc/rec_index_service_compile:1.1.6
#交互式运行
docker run -it mirrors.tencent.com/todacc/rec_index_service_compile:1.1.6  /bin/bash
```

### 保存发布
保存本次更改，类似git commit
```
docker commit -m="has update" -a="runoob" e218edb10161 mirrors.tencent.com/myspace/video_rec_annalgo:dev
```
- -m 提交的描述信息
- -a 指定镜像作者
- e218edb10161 容器ID，执行docker ps [-a]查看
- runoob/ubuntu:v2 新镜像名

发布到远端
```
docker push mirrors.tencent.com/myspace/video_rec_annalgo:dev
```
这一步可能会遇到denied: requested access to the resource is denied的错误

首先检查是否已进行docker login，如果是发布到其他仓库（非默认），需要login 指定的服务，如我这里发布到腾讯的，执行

```
docker login --username xxx --password xxx mirrors.tencent.com
```
如果这里还有问题，需要在针对对应的镜像服务进行排查，比如检查权限等。

### 镜像容器

```bash
#安装镜像
docker pull mirrors.tencent.com/todacc/rec_index_service_compile:1.1.6  /bin/bash
#查看镜像
docker images
#查看某个镜像的详细信息
docker inspect mirrors.tencent.com/todacc/rec_index_service_compile:1.1.6
#查看正在运行的容器
docker ps
#查看历史容器，包含退出的
docker ps -a
#断开后重新连接，先使用ps确定container id
docker attach ${container id}

#继续上次退出的容器。
docker ps -a // 确定container id
docker start ${container id}
docker attach ${container id}
```

## 名词解释
**IMAGE（镜像）**：
dokcer中的镜像，可以用git中的代码仓库来理解，可以从pull来使用，更改后commit/push等。
执行`docker images`查看当前有哪些镜像
以我个人的机器为例，输出如下，可以看到有REPOSITORY,TAG,IMAGE ID等关键信息：
```
REPOSITORY                                             TAG              IMAGE ID       CREATED         SIZE
mnadeem/boot-otel-tempo-provider1                      0.0.1-SNAPSHOT   32914b1c7cbe   5 months ago    168MB
mirrors.tencent.com/todacc/rec_index_service_compile   1.1.6            2462cdaa0331   7 months ago    8.53GB
mysql                                                  8.0              c0cdc95609f1   13 months ago   556MB
```
**REPOSITORY（仓库）**: 类似git的仓库的仓库地址，限定唯一一个镜像需要使用`${REPOSITORY}:${TAG}`。
有时仓库名含有镜像名，如mirrors.tencent.com/todacc/rec_index_service_compile，imrrors.tencent.com是镜像地址，todacc是命名空间或组的概念，rec_index_service_compile是具体的仓库名。

**IMAGE ID**: 镜像id，这个比较好理解，限定唯一个镜像。

**CONTAINER （容器）**: 每个镜像运行时，有一个隔离的虚拟容器运行，类似小型虚拟机，hadoop yarn的container也是这个意思。

**TAG**: 类似于git里的tag，版本的意思，使用仓库名指定image时，如果不是latest时需要指定版本。