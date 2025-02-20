---
title: Docker常用操作指南
description: Docker日常使用的常用命令和配置操作汇总
author: admin
date: 2025-02-20
sticky: 1
categories:
  - 容器
  - Docker
  - DevOps
tags:
  - docker
  - 容器化
  - 镜像加速
  - 日志配置
  - 系统管理
createTime: 2025/02/20 17:17:14
permalink: /article/cfvrkjj9/
---

# Docker常用操作指南

本文档汇总了Docker日常使用中的常用命令和配置操作，便于快速查阅。
<!-- more -->

## 1. 基本操作命令

```bash
# 拉取镜像
docker pull centos

# 删除镜像
docker rmi <镜像名称>

# 重命名/标记镜像
docker tag 原镜像名:tag 新镜像名:tag

# 查看容器日志
docker logs <容器id>
```

## 2. 配置镜像加速

在国内环境使用Docker时，建议配置镜像加速以提高下载速度。以下是阿里云镜像加速的配置方法：

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://zmkmh4t7.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

配置参考：[阿里云镜像服务文档](https://help.aliyun.com/document_detail/60750.html)

### 验证镜像加速配置

使用以下命令验证镜像加速是否配置成功：

```bash
docker info
```

查看输出中的`Registry Mirrors`部分，确认是否包含已配置的镜像地址。

## 3. Ubuntu容器初始化

在Ubuntu容器中，安装软件前建议先更新软件源：

```bash
apt update
apt install -y net-tools iputils-ping curl
```

这样可以安装一些常用的网络工具，便于排查问题。

## 4. Docker日志管理

### 全局日志配置

设置Docker容器日志的全局配置，限制日志大小和文件数量：

```bash
vim /etc/docker/daemon.json

{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}

systemctl restart docker
```

此配置会：
- 使用`json-file`作为日志驱动
- 限制单个日志文件最大为10MB
- 最多保留3个日志文件（轮转）

### 查找容器日志文件位置

如需直接访问容器的日志文件，可使用以下命令查找：

```bash
ls -lh /var/lib/docker/containers/$(docker inspect --format='{{.Id}}' 容器名)/*-json.log
```

## 5. 常用操作示例

### 运行交互式容器

```bash
docker run -it --name mycontainer ubuntu bash
```

### 后台运行容器

```bash
docker run -d --name myserver nginx
```

### 端口映射

```bash
docker run -d -p 8080:80 --name webserver nginx
```

### 挂载卷

```bash
docker run -d -v /host/path:/container/path --name datacontainer mysql
```

### 查看容器列表

```bash
# 查看运行中的容器
docker ps

# 查看所有容器（包括已停止的）
docker ps -a
```

### 启动/停止容器

```bash
docker start <容器id或名称>
docker stop <容器id或名称>
docker restart <容器id或名称>
```

### 进入运行中的容器

```bash
docker exec -it <容器id或名称> bash
```

## 6. 故障排查技巧

### 检查容器状态

```bash
docker inspect <容器id>
```

### 查看容器资源使用情况

```bash
docker stats
```

### 查看容器进程

```bash
docker top <容器id>
```

### 清理未使用的资源

```bash
# 清理悬空镜像
docker image prune

# 清理停止的容器
docker container prune

# 清理未使用的网络
docker network prune

# 清理所有未使用资源
docker system prune -a
```

## 7. 安全最佳实践

- 使用官方镜像或受信任的镜像源
- 定期更新基础镜像
- 使用非root用户运行容器
- 设置资源限制（内存、CPU）
- 不要在镜像中存储敏感信息
- 使用`--read-only`标志使容器文件系统只读
