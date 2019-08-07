---
title: docker-read
date: 2018-05-24 13:25:16
tags: doc
categories:
    - cate-summary
---

### docker 安装（mac）

- brew cask install docker
- docker –version
- docker info
- 镜像加速
  - 国内网络问题，后续拉取 Docker 镜像十分缓慢，可以配置加速器来解决。
  - docker 应用 -> Perferences -> Daemon -> Registry mirrors -> 填写镜像地址（registry.dev.xxx.com）
  - 微云上找一个node镜像地址：registry.dev.xxx.com/baidu_containers/nodejs-base:v1

## demo

1. docker run 在容器内运行一个应用程序：

   - docker run f97883e95d8d echo “echo docker”
     - f97883e95d8d： 要运行的镜像
     - echo “echo docker”: 在启动的容器里执行的命令

2. 运行交互式的容器：

   - docker run -it f97883e95d8d bash
     - -t:在新容器内指定一个伪终端或终端。
     - -i:允许你对容器内的标准输入 (STDIN) 进行交互。

3. 容器container （运行时 RUNTIME）

   - 查看container 容器系统版本

     > cat /etc/os-release

   - 查看当前系统的版本信息

     > cat /proc/version

   - 查看容器内的标准输入输出

     > docker logs 2b1b7a428627
     > docker logs CONTAINER ID/NAME

   - 停止容器：

     > docker stop dd7521aca04a

   - 重启容器：

     > docker restart dd7521aca04a
     > docker start dd7521aca04a

   - 检查容器的底层信息：

     > docker inspect dd7521aca04a

   - 删除容器：

     > docker rm dd7521aca04a

   - 退出容器：

     - exit命令 或者 CTRL+D

4. 镜像images （静态的）

   - 列出本地主机的镜像： docker images
     - REPOSITORY： 表示镜像的仓库源
     - TAG：镜像的标签
     - IMAGE ID：镜像的ID
     - CREATED：镜像创建时间
     - SIZE：镜像大小
   - 从Docker Hub搜索镜像：https://hub.docker.com/
   - 查找镜像： docker search httpd
   - 拖取镜像： docker pull httpd
   - 使用镜像： docker run httpd
   - 创建镜像：
     - 从已经创建的容器中更新镜像，并且提交这个镜像
     - 使用Dockerfile 指令来创建一个镜像
   - 构建镜像：
     - 使用命令： docker build，创建一个新的镜像。需要提前创建一个Dockfile文件，包含一组指令告诉Docker 如何构建镜像。
     - 每一个指令都会在镜像上创建一个新的层， 每一个指令的前缀必须都是大写的。
     - 第一条FFROM，指定使用哪个镜像源
     - RUN 指令 告诉docker在镜像内执行命令，安装了什么
   - 设置镜像标签
     - docker tag 860c279d2fec runoob/centos:dev
     - 镜像ID，是 860c279d2fec ,用户名称、镜像源名(repository name)和新的标签名(tag)

5. Docker容器连接：

   - 

6. 微云上发布

   - wget xxx // 获取镜像
   - unzip v0510.zip /home/work/project/xxx
   - node /home/work/project/app.js

7. 项目部署

   - 1.node/npm 依赖的环境 （固定不变的）

   - 2.node_modules （基本不变）

   - 3.src 源码 （动态变化）

   - 最后一个依赖前两个： 1 + 2

   - 制定Dockerfile

     > FROM registry.dev.weiyun.baidu.com/baidu_containers/nodejs:v1
     > COPY node_modules/ /home/work/nodejs/…..

     > FROM registry.dev.weiyun.baidu.com/baidu_containers/nodejs:v2
     > COPY src/ /home/work/nodejs/

8. Dockerfile

   - FROM egistry.dev.weiyun.baidu.com/baidu_containers/nodejs-base:v1
   - RUN cp node.js xxx.nodejs
   - RUN xxx
   - CMD node app.js
   - 每一层都是一个layer

### Docker 命令

- 容器生命周期管理
  - docker run： 创建一个新的容器并运行一个命令
  - docker start： 启动一个或多个已经被停止的容器
  - docker stop：停止一个运行中的容器
  - docker restart: 重启容器
  - docker kill：杀掉一个运行中的机器
  - docker run：删除一个多个容器
  - docker pause/unpause：暂停/恢复容器中的所有进程
  - docker create：创建一新的容器但不启动它
  - docker exec：在运行的容器中执行命令
- 容器操作
  - docker ps：列出容器（-a， 显示所有容器；-f，根据条件过滤显示的内容；-l，最近创建的容器；-q，只显示容器编号）
  - docker inspect：获取容器/镜像的元数据

### Linux 发行版排行榜

- https://zhuanlan.zhihu.com/p/33771775