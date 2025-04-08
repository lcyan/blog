---
title: UFW 和 Fail2Ban 联合防御：Linux 服务器安全加固指南
description: 详细讲解如何使用 UFW 和 Fail2Ban 共同保护 Linux 服务器，防范暴力破解和恶意访问
author: admin
date: 2025-04-08
categories:
  - linux
  - 网络安全
  - 系统管理
tags:
  - ufw
  - fail2ban
  - 防火墙
  - 安全加固
createTime: 2025/04/08 14:20:00
permalink: /article/ufw-fail2ban-security/
---

# UFW 和 Fail2Ban 联合防御：Linux 服务器安全加固指南

在现代服务器安全中，仅仅依赖防火墙是不够的。本指南将详细介绍如何结合 UFW 和 Fail2Ban 来全面保护您的 Linux 服务器。

<!-- more -->

## 1. Fail2Ban 简介

Fail2Ban 是一个入侵防御工具，它通过分析系统日志来动态阻止显示出恶意行为的 IP 地址。与 UFW 配合使用，可以显著提高服务器的安全性。

## 2. 安装 Fail2Ban

在大多数 Debian/Ubuntu 系统上，安装 Fail2Ban 非常简单：

```bash
# 更新软件包列表
sudo apt update

# 安装 Fail2Ban
sudo apt install fail2ban
```

## 3. Fail2Ban 基本配置

### 3.1 配置文件准备

Fail2Ban 的主要配置文件是 `/etc/fail2ban/jail.conf`。为了保留原始配置，我们创建一个本地配置文件：

```bash
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
```

### 3.2 基本全局设置示例

在 `jail.local` 文件中，可以配置全局默认设置：

```ini
[DEFAULT]
# 禁止时间（秒）
bantime = 3600

# 发现失败次数
maxretry = 3

# 检查时间窗口（秒）
findtime = 600

# 忽略的 IP 地址
ignoreip = 127.0.0.1/8 192.168.1.0/24
```

## 4. 常见服务的 Fail2Ban 规则

### 4.1 SSH 防护

```ini
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
```

### 4.2 Apache 防护

```ini
[apache-auth]
enabled = true
port = http,https
filter = apache-auth
logpath = /var/log/apache2/*access.log
maxretry = 3
bantime = 3600
```

### 4.3 Nginx 防护

```ini
[nginx-http-auth]
enabled = true
port = http,https
filter = nginx-http-auth
logpath = /var/log/nginx/error.log
maxretry = 3
bantime = 3600
```

## 5. UFW 与 Fail2Ban 集成

### 5.1 配置 Fail2Ban 使用 UFW

编辑 `/etc/fail2ban/action.d/ufw.conf`，确保使用 UFW 作为阻止方法：

```ini
[Init]
# UFW 阻止动作
actionstart =
actionstop =
actioncheck =
actionban = ufw insert 1 deny from <ip> to any
actionunban = ufw delete deny from <ip> to any
```

### 5.2 在 jail.local 中启用 UFW 动作

```ini
[DEFAULT]
banaction = ufw
```

## 6. 启动和管理 Fail2Ban

```bash
# 启动 Fail2Ban
sudo systemctl start fail2ban

# 设置开机自启
sudo systemctl enable fail2ban

# 查看 Fail2Ban 状态
sudo systemctl status fail2ban

# 查看被禁止的 IP
sudo fail2ban-client status sshd
```

## 7. 自定义防护规则

### 7.1 创建自定义过滤器

在 `/etc/fail2ban/filter.d/` 目录下创建自定义过滤器文件，例如 `custom.conf`：

```ini
[Definition]
failregex = ^<HOST> - - \[.*\] ".*" 404 .*$
ignoreregex =
```

### 7.2 在 jail.local 中使用自定义规则

```ini
[custom]
enabled = true
port = http,https
filter = custom
logpath = /var/log/nginx/access.log
maxretry = 5
bantime = 7200
```

## 8. 监控与调整

### 8.1 日志监控

```bash
# 实时查看 Fail2Ban 日志
sudo tail -f /var/log/fail2ban.log
```

### 8.2 性能与安全平衡

- 不要将 `maxretry` 设置得太低，以免误封锁合法用户
- 根据实际情况调整 `bantime`
- 定期检查被禁止的 IP，确认没有误判

## 9. 注意事项

> [!warning]
>
> - 谨慎配置，避免误封锁合法 IP
> - 保留一个可靠的远程访问方式
> - 定期审查和更新规则
> - 考虑使用 VPN 或跳板机访问重要服务器

## 结论

通过结合 UFW 和 Fail2Ban，您可以构建一个多层次的服务器防御系统。这种方法不仅可以有效阻止暴力破解，还能降低安全风险。

## 附加建议

1. 定期更新系统和安全工具
2. 使用强密码和密钥认证
3. 关闭不必要的服务和端口
4. 持续学习和关注最新的安全实践
