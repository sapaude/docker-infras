# Docker 常用操作命令速查表

## 一、镜像 (Images) 管理

镜像是构建容器的基础。

1. **列出本地镜像**
   ```bash
   docker images
   docker image ls # 推荐使用更明确的命令
   ```
    * `REPOSITORY`: 镜像名称
    * `TAG`: 镜像标签 (版本)
    * `IMAGE ID`: 镜像唯一ID
    * `CREATED`: 镜像创建时间
    * `SIZE`: 镜像大小

2. **拉取镜像** (从 Docker Hub 下载镜像)
   ```bash
   docker pull <image_name>:<tag>
   docker pull ubuntu:latest # 拉取最新版 Ubuntu
   docker pull nginx # 默认拉取 latest
   ```

3. **构建镜像** (从 Dockerfile 构建)
   ```bash
   docker build -t <image_name>:<tag> <path_to_dockerfile_context>
   docker build -t myapp:1.0 . # 在当前目录查找 Dockerfile 并构建名为 myapp:1.0 的镜像
   ```
    * `-t`: 指定镜像名称和标签。
    * `.`: 指定 Dockerfile 所在的上下文路径 (当前目录)。

4. **删除镜像**
   ```bash
   docker rmi <image_id_or_name>:<tag>
   docker rmi myapp:1.0
   docker rmi <image_id> # 根据 ID 删除
   ```
    * 如果镜像有被容器使用，需要先删除容器。
    * `-f`: 强制删除 (慎用，会删除所有依赖该镜像的容器)。

5. **清理悬空镜像** (没有被任何标签引用的镜像)
   ```bash
   docker image prune
   docker image prune -a # 删除所有未被使用的镜像 (包括没有标签的)
   ```

## 二、容器 (Containers) 操作

容器是镜像的运行实例。

1. **列出运行中的容器**
   ```bash
   docker ps
   docker container ls # 推荐使用更明确的命令
   ```

2. **列出所有容器** (包括已停止的)
   ```bash
   docker ps -a
   docker container ls -a
   ```

3. **运行容器**
   ```bash
   docker run [OPTIONS] <image_name>:<tag> [COMMAND] [ARG...]
   ```
    * **常用选项:**
        * `-d`: 后台运行 (detached mode)。
        * `-p <host_port>:<container_port>`: 端口映射。
            * `docker run -p 8080:80 nginx` (将宿主机 8080 映射到容器 80)
        * `--name <container_name>`: 为容器指定一个名称。
            * `docker run --name my-nginx -d nginx`
        * `-v <host_path>:<container_path>`: 卷挂载 (数据持久化)。
            * `docker run -v /data/my_app:/app -d myapp`
        * `-e <KEY>=<VALUE>`: 设置环境变量。
            * `docker run -e MY_VAR=value -d myapp`
        * `--rm`: 容器停止后自动删除。
            * `docker run --rm ubuntu echo "Hello"`
        * `-it`: 交互式运行，并分配一个伪终端 (通常用于进入容器)。
            * `docker run -it ubuntu bash` (进入 Ubuntu 容器的 bash shell)

4. **启动/停止/重启容器**
   ```bash
   docker start <container_id_or_name>
   docker stop <container_id_or_name>
   docker restart <container_id_or_name>
   ```

5. **删除容器**
   ```bash
   docker rm <container_id_or_name>
   docker rm my-nginx
   docker rm $(docker ps -aq) # 删除所有已停止的容器
   ```
    * `-f`: 强制删除运行中的容器。

6. **查看容器日志**
   ```bash
   docker logs <container_id_or_name>
   docker logs -f <container_id_or_name> # 实时跟踪日志
   docker logs --tail 100 <container_id_or_name> # 查看最后100行
   ```

7. **进入运行中的容器**
   ```bash
   docker exec -it <container_id_or_name> bash # 或 sh
   ```
    * `-it`: 交互式运行，并分配一个伪终端。

8. **查看容器详细信息**
   ```bash
   docker inspect <container_id_or_name>
   ```
    * 返回 JSON 格式的详细配置和状态信息。

9. **复制文件到容器/从容器复制文件**
   ```bash
   docker cp <host_path> <container_id_or_name>:<container_path> # 复制到容器
   docker cp ./myfile.txt my-container:/app/
   docker cp <container_id_or_name>:<container_path> <host_path> # 从容器复制
   docker cp my-container:/app/log.txt ./
   ```

10. **查看容器的资源使用情况**
    ```bash
    docker stats
    ```
    * 实时显示 CPU、内存、网络 I/O 等。

## 三、网络 (Networks) 管理

Docker 容器可以通过网络相互通信。

1. **列出所有网络**
   ```bash
   docker network ls
   ```

2. **创建网络**
   ```bash
   docker network create <network_name>
   docker network create my-app-network
   ```

3. **连接容器到网络**
   ```bash
   docker network connect <network_name> <container_id_or_name>
   docker network connect my-app-network my-web-app
   ```

4. **断开容器与网络的连接**
   ```bash
   docker network disconnect <network_name> <container_id_or_name>
   ```

5. **删除网络**
   ```bash
   docker network rm <network_name>
   ```

## 四、卷 (Volumes) 管理

卷用于持久化容器数据。

1. **列出所有卷**
   ```bash
   docker volume ls
   ```

2. **创建卷**
   ```bash
   docker volume create <volume_name>
   docker volume create my-data-volume
   ```

3. **删除卷**
   ```bash
   docker volume rm <volume_name>
   ```
    * `-f`: 强制删除。

4. **清理未使用的卷**
   ```bash
   docker volume prune
   ```

## 五、Docker Compose

用于定义和运行多容器 Docker 应用。

1. **启动服务** (在 `docker-compose.yml` 所在目录执行)
   ```bash
   docker-compose up
   docker-compose up -d # 后台运行
   ```

2. **停止服务**
   ```bash
   docker-compose stop
   ```

3. **停止并删除容器、网络、卷** (但不会删除镜像)
   ```bash
   docker-compose down
   ```

4. **查看服务状态**
   ```bash
   docker-compose ps
   ```

5. **查看服务日志**
   ```bash
   docker-compose logs [service_name]
   docker-compose logs -f web # 实时跟踪 web 服务的日志
   ```

6. **构建服务镜像** (如果 `docker-compose.yml` 中有 `build` 指令)
   ```bash
   docker-compose build [service_name]
   ```

## 六、系统级命令

1. **查看 Docker 版本信息**
   ```bash
   docker version
   ```

2. **查看 Docker 系统信息**
   ```bash
   docker info
   ```

3. **清理 Docker 系统** (删除所有停止的容器、未使用的网络、悬空镜像和构建缓存)
   ```bash
   docker system prune
   docker system prune -a # 删除所有未使用的镜像 (包括没有标签的)
   docker system prune --volumes # 同时删除未使用的卷 (慎用)
   ```