---
title: Docker 常用命令
slug: docker-chang-yong-ming-ling
cover: ""
categories: []
tags: []
halo:
  site: https://blog.kangyaocoding.top
  name: d3f0244b-c5c9-4d0f-b61b-10a4b1967cfd
  publish: false
---
## 简介

Docker 是一种流行的容器化平台，常用于开发、打包和部署应用。

以下是一些我在开发中遇到的常用的 Docker 命令及其用途，希望能够帮助大家快速记起当时学习Docker的时候：

### 镜像操作
1. **从仓库中拉取镜像**
   ```bash
   docker pull [image_name]
   ```
   如：
   ```bash
   docker pull nginx
   ```

2. **列出本地所有镜像**
   ```bash
   docker images
   ```

3. **删除本地镜像**
   ```bash
   docker rmi [image_id]
   ```
   如：
   ```bash
   docker rmi nginx
   ```

### 容器操作
4. **运行容器**
   ```bash
   docker run [options] [image_name]
   ```
   常用的选项：
   - `-d`：后台运行容器
   - `-p`：端口映射
   - `--name`：容器命名
   如：
   ```bash
   docker run -d -p 80:80 --name mynginx nginx
   ```

5. **列出运行中的容器**
   ```bash
   docker ps
   ```

6. **列出所有容器（包括停止的）**
   ```bash
   docker ps -a
   ```

7. **停止容器**
   ```bash
   docker stop [container_id]
   ```
   如：
   ```bash
   docker stop mynginx
   ```

8. **启动停止的容器**
   ```bash
   docker start [container_id]
   ```
   如：
   ```bash
   docker start mynginx
   ```

9. **删除容器**
   ```bash
   docker rm [container_id]
   ```
   如：
   ```bash
   docker rm mynginx
   ```

### 数据卷
10. **创建数据卷**
    ```bash
    docker volume create [volume_name]
    ```
    如：
    ```bash
    docker volume create myvolume
    ```

11. **列出数据卷**
    ```bash
    docker volume ls
    ```

12. **删除数据卷**
    ```bash
    docker volume rm [volume_name]
    ```
    如：
    ```bash
    docker volume rm myvolume
    ```

### 网络
13. **创建网络**
    ```bash
    docker network create [network_name]
    ```
    如：
    ```bash
    docker network create mynetwork
    ```

14. **列出网络**
    ```bash
    docker network ls
    ```

15. **删除网络**
    ```bash
    docker network rm [network_name]
    ```
    如：
    ```bash
    docker network rm mynetwork
    ```

### 日志与调试
16. **查看容器日志**
    ```bash
    docker logs [container_id]
    ```
    如：
    ```bash
    docker logs mynginx
    ```

17. **进入运行中的容器**
    ```bash
    docker exec -it [container_id] /bin/bash
    ```
    如：
    ```bash
    docker exec -it mynginx /bin/bash
    ```

### Dockerfile 与镜像构建
18. **构建镜像**
    ```bash
    docker build -t [image_name] [path_to_dockerfile]
    ```
    如：
    ```bash
    docker build -t myapp:latest .
    点（.）表示从当前目录读取 Dockerfile
    ```

19. **查看镜像历史**
    ```bash
    docker history [image_name]
    ```
    如：
    ```bash
    docker history nginx
    ```

参考[Docker Docs](https://docs.docker.com/)

如果您觉得今天的文章对您有帮助，我相信您一定会喜欢我的博客。
[哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你](https://blog.kangyaocoding.top/ "哈利の小屋 - Kang Yao Coding - 努力有时候战胜不了天分，但至少能让别人看得起你")。
在那里，我会定期更新关于计算机类的文章，并与您分享更多实用的经验和知识。欢迎您来访问和留言交流。