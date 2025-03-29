---
created: 2025-03-22T23:10
updated: 2024-05-23T14:06
创建时间: 2025-03-22T23:10
更新时间: 2024-05-23T14:06
---
# Docker学习

## 什么是容器

容器是一种虚拟化技术，用于在操作系统级别隔离应用程序及其依赖环境的运行环境。与传统的虚拟机相比，容器更加轻量级、快速和灵活。

容器包含了应用程序及其所有依赖项，如代码、运行时环境、系统工具、系统库等，以及所需的配置文件。容器将这些组件打包到一个独立的单元中，并与宿主操作系统共享内核，从而实现了资源的高效利用和快速部署。

容器技术的主要特点包括：

1. **轻量级：** 由于容器与宿主操作系统共享内核，因此它们不需要额外的操作系统镜像，使得容器比传统虚拟机更加轻量级。
2. **快速启动：** 容器可以在几秒钟内启动，因为它们不需要像虚拟机那样启动整个操作系统，而是直接在宿主操作系统上启动应用程序。
3. **隔离性：** 每个容器都运行在独立的环境中，与其他容器和宿主系统隔离开来，从而确保了应用程序之间的互相独立和安全性。
4. **可移植性：** 容器提供了一种标准化的打包和交付机制，使得应用程序可以在任何支持容器的平台上以相同的方式运行，无需担心环境差异。
5. **可扩展性：** 容器可以根据需要进行水平或垂直扩展，以满足应用程序的需求，而且扩展过程通常非常快速和灵活。

容器架构是指容器技术所涉及的组件和系统结构，用于管理、运行和部署容器化应用程序。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/容器架构图.png" alt="容器架构图" style="zoom:67%;" />

典型的容器架构包括以下组件：

1. **宿主机（Host Machine）：** 宿主机是运行容器的物理或虚拟计算机。它可以是云服务器、物理服务器或者虚拟机实例。宿主机上安装了容器运行时（如 Docker Engine），负责管理容器的生命周期。
2. **容器引擎（Container Engine）：** 容器引擎是一个用于创建、运行和管理容器的软件。最流行的容器引擎是 Docker，但也有其他选择，如 Containerd、CRI-O 等。容器引擎负责与宿主机的操作系统内核交互，以便在其中创建和管理容器。
3. **镜像（Image）：** 镜像是容器运行时的静态文件，包含了应用程序的代码、运行时环境、系统工具和依赖项。镜像是容器的基础，用于创建容器实例。Docker 镜像通常由 Dockerfile 构建而成，可以通过 Docker Hub 或私有镜像仓库分享和分发。
4. **容器（Container）：** 容器是运行在宿主机上的一个或多个镜像的实例。每个容器都是一个独立的运行环境，包含了应用程序及其所有依赖项，以及所需的配置文件。容器之间是相互隔离的，但可以与宿主机共享资源和网络。
5. **容器编排（Container Orchestration）：** 容器编排是一种自动化和管理容器化应用程序的方法。它涉及到部署、扩展、管理和调度容器的过程，以确保应用程序的高可用性、负载均衡和弹性。常见的容器编排工具包括 Kubernetes、Docker Swarm 等。
6. **容器注册中心（Container Registry）：** 容器注册中心是用于存储、管理和分发容器镜像的中心化服务。它允许开发人员和运维团队共享和获取镜像，以便在不同的环境中部署应用程序。常见的容器注册中心包括 Docker Hub、Harbor、Quay 等。

容器技术的代表性实现包括 Docker、Kubernetes、Podman 等。这些工具使得容器的创建、管理和部署变得更加简单和高效，广泛应用于软件开发、测试、部署和运维等领域。



## 什么是Docker

Docker 是一种基于容器化技术的开源平台，用于开发、交付和运行应用程序。它允许开发人员将应用程序及其所有依赖项（如库、运行时环境、配置文件等）打包到一个称为 Docker 镜像的可移植容器中。这些容器可以在任何支持 Docker 的环境中以相同的方式运行，无需担心环境差异或依赖问题。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/Docker架构图.png" alt="Docker架构图" style="zoom: 50%;" />

Docker 的核心组件包括：

1. **Docker 引擎（Docker Engine）：** Docker 引擎是 Docker 平台的核心组件，负责管理容器的生命周期。它包括一个守护进程（Docker Daemon）和一个命令行工具（Docker CLI），可以通过 Docker CLI 发送命令给 Docker Daemon，以便创建、运行、停止和删除容器等操作。
2. **Docker 镜像（Docker Image）：** Docker 镜像是用于创建容器的静态文件，包含了应用程序的代码、运行时环境、系统工具和依赖项。镜像可以通过 Dockerfile 定义和构建，然后推送到 Docker Hub 或私有镜像仓库进行分享和分发。
3. **Docker 容器（Docker Container）：** Docker 容器是 Docker 镜像的运行实例，每个容器都是一个独立的运行环境，包含了应用程序及其所有依赖项，以及所需的配置文件。容器可以在任何支持 Docker 的环境中以相同的方式运行，实现了环境的一致性和可移植性。
4. **Docker Hub：** Docker Hub 是 Docker 公司提供的官方镜像仓库，用于存储、管理和分享 Docker 镜像。开发人员可以在 Docker Hub 上找到各种官方和社区维护的 Docker 镜像，也可以将自己构建的镜像推送到 Docker Hub 进行分享。

Docker 技术的主要优点包括轻量级、快速、灵活、可移植和一致的运行环境。它在现代软件开发、测试、部署和运维中得到了广泛的应用，成为了构建微服务架构和实现持续集成/持续部署的重要工具之一。

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/Docker使用.png" alt="Docker使用" style="zoom: 50%;" />



## Docker安装

### 在 Linux 上安装 Docker

不同的 Linux 发行版可能有不同的包管理工具和安装步骤。下面我将分别介绍在常见的几种 Linux 发行版上安装 Docker 的方法：

#### 在 Ubuntu 上安装 Docker

在 Ubuntu 上安装 Docker 可以通过 apt 包管理器进行，按照以下步骤操作：

1. 更新软件包索引：

```
sudo apt update
```

2. 安装所需的软件包，以支持通过 HTTPS 使用 Docker 仓库：

```
sudo apt install apt-transport-https ca-certificates curl software-properties-common
```

3. 添加 Docker 的官方 GPG 密钥：

```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

4. 添加 Docker 的 APT 仓库：

```
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

5. 更新软件包索引：

```
sudo apt update
```

6. 安装 Docker：

```
sudo apt install docker-ce
```

7. 验证 Docker 是否安装成功：

```
sudo docker --version
```

![Docker安装succ](http://images.xiaohai-hx.cn/复习笔记/面试题/Docker安装succ.png)



#### 在 CentOS 上安装 Docker

在 CentOS 上安装 Docker 可以通过 yum 包管理器进行，按照以下步骤操作：

1. 更新软件包索引：

```
sudo yum check-update
```

2. 安装所需的软件包，以支持通过 HTTPS 使用 Docker 仓库：

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

3. 添加 Docker 的官方 YUM 仓库：

```
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

4. 安装 Docker：

```
sudo yum install docker-ce
```

5. 启动 Docker 服务：

```
sudo systemctl start docker
```

6. 设置 Docker 开机自启动：

```
sudo systemctl enable docker
```

7. 验证 Docker 是否安装成功：

```
sudo docker --version
```



#### 在 Fedora 上安装 Docker

在 Fedora 上安装 Docker 可以通过 dnf 包管理器进行，按照以下步骤操作：

1. 更新软件包索引：

```
sudo dnf check-update
```

2. 安装所需的软件包，以支持通过 HTTPS 使用 Docker 仓库：

```
sudo dnf install -y dnf-plugins-core
```

3. 添加 Docker 的官方 YUM 仓库：

```
sudo dnf config-manager --add-repo https://download.docker.com/linux/fedora/docker-ce.repo
```

4. 安装 Docker：

```
sudo dnf install docker-ce
```

5. 启动 Docker 服务：

```
sudo systemctl start docker
```

6. 设置 Docker 开机自启动：

```
sudo systemctl enable docker
```

7. 验证 Docker 是否安装成功：

```
sudo docker --version
```

以上是在 Ubuntu、CentOS 和 Fedora 上安装 Docker 的基本步骤。请注意，具体的安装步骤可能因 Linux 发行版版本、软件包版本和系统配置而有所不同。安装前请务必阅读官方文档并遵循官方建议。



### 在 macOS 上安装 Docker

在 macOS 上安装 Docker 通常可以通过 Docker Desktop 来完成，按照以下步骤操作：

1. 前往 Docker 官网下载页面：https://www.docker.com/products/docker-desktop

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/docker官网.png" alt="docker官网" style="zoom:50%;" />

1. 下载并安装 Docker Desktop，根据提示进行操作。
2. 安装完成后，启动 Docker Desktop。
3. 在启动后，Docker 图标将出现在菜单栏中。单击 Docker 图标并选择“Preferences”，然后在“Resources”选项卡中分配足够的资源（CPU、内存等）给 Docker。
4. 验证 Docker 是否安装成功：

在终端中执行以下命令：

```
docker --version
```



### 在 Windows 上安装 Docker

在 Windows 上安装 Docker 也可以通过 Docker Desktop 来完成，步骤如下：

1. 前往 Docker 官网下载页面：https://www.docker.com/products/docker-desktop

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/docker官网.png" alt="docker官网" style="zoom:50%;" />

1. 下载并安装 Docker Desktop，根据提示进行操作。
2. 安装完成后，启动 Docker Desktop。
3. 在启动后，Docker 图标将出现在任务栏中。右键单击 Docker 图标并选择“Settings”，然后在“Resources”选项卡中分配足够的资源（CPU、内存等）给 Docker。
4. 验证 Docker 是否安装成功：

在 PowerShell 或命令提示符中执行以下命令：

```
docker --version
```

以上是在 Linux、macOS 和 Windows 上安装 Docker 的基本步骤。

> 请注意，安装过程可能会因操作系统版本、系统配置和网络状态等因素而有所不同。安装前请务必阅读官方文档并遵循官方建议。



## Docker命令

### Docker容器命令

|                           命令                            |                描述                |
| :-------------------------------------------------------: | :--------------------------------: |
|                        `docker ps`                        |        列出所有运行中的容器        |
|                      `docker ps -a`                       |     列出所有容器（不考虑状态）     |
|                      `docker ps -s`                       |   列出所有运行中的容器及文件大小   |
|                      `docker ps -q`                       |        列出运行中容器的 ID         |
|                      `docker ps -aq`                      |  列出所有容器的 ID（不考虑状态）   |
|             `docker ps --filter "key=value"`              |            过滤容器列表            |
|                  `docker run image_name`                  |      创建新容器从 Docker 镜像      |
|       `docker run --name container_name image_name`       | 创建新容器从 Docker 镜像，固定名称 |
|            `docker start container_name_or_id`            |              启动容器              |
|            `docker stop container_name_or_id`             |          停止运行中的容器          |
|           `docker restart container_name_or_id`           |            重新启动容器            |
|            `docker pause container_name_or_id`            |          暂停运行中的容器          |
|           `docker unpause container_name_or_id`           |          恢复已暂停的容器          |
|                  `docker run image_name`                  |              运行容器              |
|                `docker run -it image_name`                |        在交互模式下运行容器        |
|                `docker run -d image_name`                 |        在后台模式下运行容器        |
|               `docker run --rm image_name`                |          删除容器在退出时          |
|     `docker exec -it container_name_or_id /bin/bash`      |          进入运行中的容器          |
|    `docker run -p host_port:container_port image_name`    |           映射容器的端口           |
|   `docker rename old_container_name new_container_name`   |             重命名容器             |
| `docker cp container_name_or_id:/path/to/file /host/path` |        从容器复制文件到主机        |
| `docker cp /host/path container_name_or_id:/path/to/file` |        从主机复制文件到容器        |
|             `docker rm container_name_or_id`              |              删除容器              |
|                 `docker container prune`                  |          删除已停止的容器          |
|                `docker container prune -f`                |     删除已停止和正在运行的容器     |



### Docker镜像命令

|                        命令                         |                 描述                  |
| :-------------------------------------------------: | :-----------------------------------: |
|                   `docker images`                   |             列出所有镜像              |
|                 `docker images -a`                  |     列出所有镜像（包括悬挂镜像）      |
|                 `docker images -q`                  |              列出镜像 ID              |
|           `docker build -t image_name .`            |               构建镜像                |
|         `docker build -t image_name:tag .`          |        构建镜像，使用不同标签         |
| `docker build -t image_name -f Dockerfile_custom .` | 构建镜像，使用自定义命名的 Dockerfile |
|             `docker history image_name`             |             显示镜像历史              |
|     `docker tag old_image_name new_image_name`      |            重命名现有镜像             |
|               `docker rmi image_name`               |               删除镜像                |



### Docker网络命令

| 命令                                                         |           描述           |
| :----------------------------------------------------------- | :----------------------: |
| `docker network ls`                                          |       列出所有网络       |
| `docker network create network_name`                         |         创建网络         |
| `docker network inspect network_name`                        |       显示网络信息       |
| `docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' container_name_or_id` | 获取运行中容器的 IP 地址 |
| `docker network connect network_name container_name_or_id`   |      连接容器到网络      |
| `docker run --network=network_name image_name`               |  连接容器到网络，启动时  |
| `docker network disconnect network_name container_name_or_id` |   断开容器与网络的连接   |
| `docker network rm network_name`                             |         删除网络         |



### Docker卷命令

| 命令                                                         |              描述               |
| :----------------------------------------------------------- | :-----------------------------: |
| `docker volume create volume_name`                           |             创建卷              |
| `docker volume ls`                                           |           列出所有卷            |
| `docker run -v volume_name:/container/path image_name`       |       挂载卷使用 -v 标志        |
| `docker run --mount source=volume_name,target=/container/path image_name` |     挂载卷使用 --mount 标志     |
| `docker volume inspect volume_name`                          |         获取卷详细信息          |
| `docker volume rm volume_name`                               |             删除卷              |
| `docker run -v /host/path:/container/path image_name`        |       使用绑定挂载挂载卷        |
| `docker run --mount type=bind,source=/host/path,target=/container/path image_name` | 创建绑定挂载卷使用 --mount 标志 |



### Docker Registry 命令

|                    命令                    |         描述          |
| :----------------------------------------: | :-------------------: |
|               `docker login`               |    登录 Docker Hub    |
| `docker push registry_name/image_name:tag` | 将镜像推送到 Registry |
| `docker pull registry_name/image_name:tag` | 从 Registry 下载镜像  |



### 系统级 Docker 命令

|                命令                |                 描述                 |
| :--------------------------------: | :----------------------------------: |
|           `docker info`            |           获取 Docker 信息           |
|           `docker stats`           |      获取正在运行容器的统计信息      |
|   `docker stats $(docker ps -q)`   |        获取所有容器的统计信息        |
|          `docker version`          |           显示 Docker 版本           |
| `docker inspect object_name_or_id` | 获取详细对象信息（容器、镜像、卷等） |
|         `docker system df`         |       获取 Docker 使用情况摘要       |
|       `docker system prune`        |           清理 Docker 系统           |



> 参考文章：
>
> [微信公众号-全网对Docker命令总结最全的文章，秒收藏！](https://mp.weixin.qq.com/s/R2-tFb3uXLp3ZTUvHpSPOA)