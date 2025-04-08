---
title: UFW (Uncomplicated Firewall) 使用教程
description: Linux 系统防火墙管理的全面指南，详细介绍 UFW 的安装、配置和使用
author: admin
date: 2025-04-08
categories:
  - linux
  - 网络安全
  - 系统管理
tags:
  - ufw
  - 防火墙
  - 网络安全
createTime: 2025/04/08 10:30:00
permalink: /article/ufw-guide/
---

# UFW (Uncomplicated Firewall) 使用教程

本指南提供了在 Linux 系统中使用 UFW（Uncomplicated Firewall）进行防火墙管理的 comprehensive 指南。

<!-- more -->

## 什么是 UFW？

UFW (Uncomplicated Firewall) 是 Linux 系统上的一个简单易用的防火墙配置工具，主要用于 Ubuntu 和 Debian 系统。它提供了一个简单的命令行界面，可以轻松管理 iptables 防火墙规则。

## 安装 UFW

在大多数 Ubuntu 和 Debian 系统上，UFW 已经预装。如果没有，可以使用以下命令安装：

```bash
sudo apt update
sudo apt install ufw
```

## 基本命令

### 启用和禁用

```bash
# 启用 UFW
sudo ufw enable

# 禁用 UFW
sudo ufw disable

# 检查 UFW 状态
sudo ufw status
```

### 默认策略设置

```bash
# 设置默认入站拒绝策略
sudo ufw default deny incoming

# 设置默认出站允许策略
sudo ufw default allow outgoing
```

## 端口和服务管理

### 允许特定端口

```bash
# 允许 SSH 端口
sudo ufw allow 22/tcp

# 允许 HTTP 端口
sudo ufw allow 80/tcp

# 允许 HTTPS 端口
sudo ufw allow 443/tcp
```

### 允许服务

```bash
# 允许 SSH
sudo ufw allow ssh

# 允许 Apache
sudo ufw allow apache

# 允许 Nginx
sudo ufw allow nginx
```

## 高级规则

### IP 地址控制

```bash
# 允许特定 IP 访问
sudo ufw allow from 192.168.1.100

# 允许特定 IP 访问特定端口
sudo ufw allow from 192.168.1.100 to any port 22
```

### 规则管理

```bash
# 删除特定规则
sudo ufw delete allow 80/tcp

# 查看规则并按序号删除
sudo ufw status numbered
sudo ufw delete 2  # 删除序号为2的规则
```

## 安全增强

### 连接限制

```bash
# 限制 SSH 连接，防止暴力破解
sudo ufw limit ssh
```

### 日志记录

```bash
# 启用日志记录
sudo ufw logging on

# 查看日志
sudo tail -f /var/log/ufw.log
```

## 系统重置

```bash
# 重置所有 UFW 规则
sudo ufw reset
```

## 注意事项

> [!warning]
>
> - 在修改防火墙规则时务必谨慎，避免意外锁定系统访问。
> - 始终保持 SSH 端口开放，确保远程管理可用。
> - 对于复杂的防火墙需求，可能需要使用更高级的工具或直接配置 iptables。

## 最佳实践

1. 定期审查和更新防火墙规则
2. 仅开放必要的端口
3. 结合其他安全措施，如 fail2ban
4. 在生产环境中进行充分测试

## 结论

UFW 提供了一个简单直观的方式来管理 Linux 系统的防火墙。通过掌握这些命令，您可以显著提高系统的安全性。
