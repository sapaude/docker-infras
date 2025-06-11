# For Alpine支持APT工具的容器一些常用工具快速安装

## 快速开始

### 精简版本

```shell
# 1. 更新包列表
apt update

apt install -y \
    procps \
    htop \
    iproute2 \
    net-tools \
    dnsutils \
    iputils-ping \
    curl \
    wget \
    less \
    vim
    
# 3. 清理 apt 缓存 (可选，但推荐在 Dockerfile 中执行，以减小镜像大小)
apt clean
rm -rf /var/lib/apt/lists/*
```

### 完整版本

```shell
# 1. 更新包列表
apt update

# 2. 安装常用工具
apt install -y \
    procps \
    htop \
    iproute2 \
    net-tools \
    dnsutils \
    iputils-ping \
    curl \
    wget \
    less \
    vim \
    tree \
    zip \
    unzip \
    lsof \
    strace

# 3. 清理 apt 缓存 (可选，但推荐在 Dockerfile 中执行，以减小镜像大小)
apt clean
rm -rf /var/lib/apt/lists/*
```

## 也可以在Dockerfile支持

> 每次构建镜像时，这些工具都会被包含进去，确保了环境的一致性和可重复性。

```dockerfile
# 假设你的基础镜像是 debian:stable-slim
FROM debian:stable-slim

# 安装常用工具
RUN apt update && \
    apt install -y --no-install-recommends \
        procps \
        iproute2 \
        net-tools \
        dnsutils \
        iputils-ping \
        curl \
        wget \
        less \
        vim-tiny \
        tree \
        zip \
        unzip \
        lsof \
        strace && \
    rm -rf /var/lib/apt/lists/* 

# ... 你的其他 Dockerfile 指令 ...
```

## 详细说明

### 进程管理和系统信息

好的，当你进入一个 Docker 容器内部，发现 `ps`、`netstat` 等常用命令缺失时，这通常是因为容器基于一个精简的 Linux 发行版（如 Alpine Linux 或 Debian Slim）构建，它们为了减小镜像体积，默认只包含了最核心的工具。

既然你发现系统有 `apt` 命令，那说明这个容器是基于 Debian/Ubuntu 系列的发行版。我们可以使用 `apt` 来安装一些在容器调试和日常操作中非常实用的工具。

**重要提示：**

* **临时性:** 在容器内部安装的工具，如果容器被删除或重新创建，这些安装的工具也会丢失。这通常用于临时调试。
* **镜像构建:** 如果你需要这些工具作为容器的长期组成部分，**强烈建议**将它们添加到你的 `Dockerfile` 中，在镜像构建阶段就安装好，而不是手动进入容器安装。这样可以保证环境的一致性和可重复性。
* **权限:** 你可能需要 `root` 权限来执行 `apt` 命令。

---

#### 1. 更新包列表

在安装任何新工具之前，总是先更新包列表，以确保你获取到的是最新版本的软件包信息。

```bash
apt update
```

#### 2. 进程管理和系统信息

* **`procps` (包含 `ps`, `top`, `free`, `uptime` 等)**
    * `ps`: 查看当前运行的进程。
    * `top`: 实时查看系统资源使用情况和进程。
    * `free`: 查看内存使用情况。
    * `uptime`: 查看系统运行时间。
  ```bash
  apt install -y procps
  ```

* **`htop` (更友好的 `top` 替代品)**
    * 提供更直观的交互式进程查看界面。
  ```bash
  apt install -y htop
  ```

#### 3. 网络工具

* **`iproute2` (包含 `ip`, `ss` 等)**
    * `ip`: 现代 Linux 网络配置工具，替代了大部分 `ifconfig` 和 `route` 功能。
    * `ss`: 查看套接字统计信息，比 `netstat` 更快更强大。
  ```bash
  apt install -y iproute2
  ```

* **`net-tools` (包含 `netstat`, `ifconfig`, `route` 等)**
    * 虽然 `iproute2` 是推荐的现代替代品，但 `net-tools` 仍然非常常用，尤其是在习惯了旧命令的情况下。
    * `netstat`: 查看网络连接、路由表、接口统计等。
    * `ifconfig`: 配置和查看网络接口。
    * `route`: 查看和修改路由表。
  ```bash
  apt install -y net-tools
  ```

* **`dnsutils` (包含 `dig`, `nslookup` 等)**
    * `dig`: 强大的 DNS 查询工具。
    * `nslookup`: 另一个 DNS 查询工具。
  ```bash
  apt install -y dnsutils
  ```

* **`ping` (网络连通性测试)**
    * 测试网络连通性。
  ```bash
  apt install -y iputils-ping # 或 ping
  ```

* **`curl` / `wget` (HTTP 客户端)**
    * `curl`: 强大的命令行工具，用于传输数据，支持多种协议（HTTP, HTTPS, FTP, SCP, SFTP 等）。常用于测试 API 接口。
    * `wget`: 另一个命令行下载工具。
  ```bash
  apt install -y curl wget
  ```

* **`tcpdump` (网络抓包)**
    * 用于捕获和分析网络数据包，进行网络故障排查。
  ```bash
  apt install -y tcpdump
  ```

#### 4. 文件和文本处理

* **`less` (分页查看文件)**
    * 比 `cat` 更适合查看大文件，支持上下滚动、搜索等。
  ```bash
  apt install -y less
  ```

* **`vim` / `nano` (文本编辑器)**
    * `vim`: 强大的命令行文本编辑器，学习曲线较陡峭。
    * `nano`: 更简单易用的命令行文本编辑器。
  ```bash
  apt install -y vim # 或 nano
  ```

* **`tree` (目录树状显示)**
    * 以树状结构显示目录内容。
  ```bash
  apt install -y tree
  ```

#### 5. 压缩/解压缩工具

* **`zip` / `unzip`**
  ```bash
  apt install -y zip unzip
  ```

* **`gzip` / `bzip2` / `xz-utils`** (通常已包含在基础镜像中，但以防万一)
  ```bash
  apt install -y gzip bzip2 xz-utils
  ```

#### 6. 其他实用工具

* **`sudo` (权限管理)**
    * 如果你需要以非 root 用户身份运行命令，但又需要临时提升权限。
  ```bash
  apt install -y sudo
  ```

* **`lsof` (列出打开的文件)**
    * 查看哪些进程正在使用哪些文件或网络连接。
  ```bash
  apt install -y lsof
  ```

* **`strace` (跟踪系统调用)**
    * 用于诊断程序行为，查看程序与内核的交互。
  ```bash
  apt install -y strace
  ```
