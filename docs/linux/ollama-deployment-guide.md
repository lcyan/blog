---
title: Ollama 大模型部署指南
description: 在Linux系统上安装、配置和运行Ollama大语言模型的完整教程
author: admin
date: 2025-02-20
categories:
  - AI
  - 大模型
  - 部署
tags:
  - ollama
  - LLM
  - gemma
  - 模型部署
  - AI推理
createTime: 2025/02/20 17:05:18
permalink: /article/aa5oqnrh/
---

# Ollama 大模型部署指南

本文档提供在Linux系统上安装、配置和运行Ollama大语言模型的完整步骤指南。

<!-- more -->

## 1. 安装Ollama

使用官方安装脚本一键安装：

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

安装过程输出示例：
```
>>> Downloading ollama...
######################################################################## 100.0%
>>> Installing ollama to /usr/local/bin...
[sudo] user 的密码：
>>> Creating ollama user...
>>> Adding ollama user to video group...
>>> Adding current user to ollama group...
>>> Creating ollama systemd service...
>>> Enabling and starting ollama service...
Created symlink from /etc/systemd/system/default.target.wants/ollama.service to /etc/systemd/system/ollama.service.
WARNING: Unable to detect NVIDIA GPU. Install lspci or lshw to automatically detect and install NVIDIA CUDA drivers.
```

> **注意**：如果需要GPU加速，请确保已安装NVIDIA驱动和CUDA。

## 2. 修改模型存放位置

默认情况下，Ollama将模型文件存储在用户目录下。可以通过以下步骤修改存储位置：

### 创建新的存储目录

```bash
mkdir -p /app/ollama/.ollama/models
chown -R ollama:ollama /app/ollama
```

### 修改systemd服务配置

编辑Ollama服务文件：

```bash
vim /etc/systemd/system/ollama.service
```

在`[Service]`部分添加以下环境变量：

```ini
Environment="OLLAMA_HOST=0.0.0.0"
Environment="OLLAMA_MODELS=/app/ollama/.ollama/models"
```

> **提示**：设置`OLLAMA_HOST=0.0.0.0`允许从其他设备访问Ollama服务。

### 重新加载并启动服务

```bash
sudo systemctl daemon-reload
sudo systemctl enable ollama
sudo systemctl start ollama
```

## 3. 运行模型

安装配置完成后，可以运行预训练模型：

```bash
ollama run gemma:2b
```

## 常见问题排查

1. **无法检测到GPU**：确保已安装`lspci`或`lshw`工具，并正确安装NVIDIA驱动
2. **权限问题**：确保当前用户已添加到`ollama`组
3. **模型下载失败**：检查网络连接和代理设置
4. **服务无法启动**：使用`systemctl status ollama`查看详细错误信息

## 高级用法

- 使用`ollama pull <model>`下载模型但不运行
- 使用`ollama list`查看已下载的模型
- 使用`ollama rm <model>`删除不需要的模型
- 通过REST API与Ollama交互：`curl -X POST http://localhost:11434/api/generate -d '{"model": "gemma:2b", "prompt": "你好"}'`

## 系统要求

- 支持的操作系统：Linux (Debian, Ubuntu, CentOS, RHEL, Fedora)
- 最低硬件要求：4GB RAM，10GB存储空间
- 推荐配置：16GB+ RAM，NVIDIA GPU，50GB+存储空间
