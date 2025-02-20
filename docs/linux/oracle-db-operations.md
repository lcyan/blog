---
title: Oracle 数据库基本操作指南
description: Oracle 数据库启动、关闭及监听状态查看的基本命令
author: admin
date: 2025-02-20
categories:
  - 数据库
  - oracle
  - 管理
tags:
  - oracle
  - 启动
  - 关闭
  - 监听器
  - 数据库管理
createTime: 2025/02/20 16:53:32
permalink: /article/1zxcp5db/
---

# Oracle 数据库基本操作指南

本文档提供 Oracle 数据库基本操作的命令参考，包括启动数据库、停止数据库以及查看监听器状态。

<!-- more -->

## 启动 Oracle 数据库

按照以下步骤启动 Oracle 数据库：

```bash
# 切换到 oracle 用户
su - oracle

# 启动 Oracle 监听服务
lsnrctl start

# 使用 SYSDBA 权限登录 SQL*Plus 并启动数据库
sqlplus / as sysdba
startup
```

## 停止 Oracle 数据库

按照以下步骤停止 Oracle 数据库：

```bash
# 停止 Oracle 监听服务
lsnrctl stop

# 停止 Oracle 数据库服务
sqlplus /nolog
conn / as sysdba
shutdown immediate
exit
```

## 查看监听器状态

使用以下命令查看 Oracle 监听器的当前状态：

```bash
lsnrctl status
```

## 注意事项

- 确保在执行这些命令前已切换至 Oracle 用户或具有足够权限
- `shutdown immediate` 会等待当前会话完成后再关闭数据库
- 也可以使用 `shutdown abort`（立即终止）或 `shutdown normal`（等待所有用户断开连接）
- 定期检查监听器状态可以帮助排查连接问题
