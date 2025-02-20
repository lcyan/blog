---
title: Windows WSL安装与管理指南
description: Windows子系统Linux(WSL)的安装、配置和管理完整教程
author: admin
date: 2025-02-20
categories:
  - windows
  - linux
  - wsl
tags:
  - wsl
  - ubuntu
  - windows子系统
  - 虚拟化
  - 系统管理
createTime: 2025/02/20 17:00:41
permalink: /article/1phrjoxf/
---

# Windows WSL安装与管理指南

本文档提供在Windows操作系统上安装、配置和管理WSL(Windows Subsystem for Linux)的完整步骤和常用命令。

<!-- more -->

## 安装Linux子系统

### 1. 开启Hyper-V

在"启用或关闭Windows功能"对话框中开启Hyper-V功能。

### 2. 设置默认版本

```powershell
wsl --set-default-version 2
```

### 3. 安装Ubuntu

```powershell
wsl --list --online
wsl --install -d Ubuntu
```

### 4. 重启并安装Linux内核更新包

下载地址：https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi

## 常用操作命令

### 5. 列出已安装的Linux发行版

```powershell
wsl -l -v
```

### 6. 删除指定的安装

```powershell
wsl --unregister Ubuntu
```

### 7. 导出/备份

```powershell
wsl --export Ubuntu d:\data\backup\Ubuntu.tar
```

### 8. 导入/还原/利用备份创建新的安装

```powershell
# 还原出来一个名字为anaconda的Linux子系统，基于备份Ubuntu
wsl --import Anaconda D:\data\wsl_data\Anaconda D:\data\backup\Ubuntu.tar
```

### 9. 启动指定的安装

```powershell
wsl -d Anaconda --user yanlc
```

### 10. 关闭指定的安装

```powershell
wsl --terminate Anaconda
```

### 11. 设置默认登录用户

```powershell
ubuntu.exe config --defualt-user yanleichang
```

### 12. 设置默认分发分支

```powershell
wsl -s Ubuntu
```

## 高级配置

### 13. 开启systemctl

```bash
# 进入debian系统后
# 编辑 /etc/wsl.conf文件，不存在就新建
[boot]
systemd=true
```

### 14. 配置WSL开机启动

参考：https://www.cnblogs.com/wswind/p/17201979.html

方法如下：
1. WIN+R 运行 `shell:startup` 打开启动目录
2. 在此目录中创建文件 `wsl-startup.vbs`
3. 在 `wsl-startup.vbs` 中填充如下内容，Arch需替换为你使用的发行版名称：

```vbscript
set ws=wscript.CreateObject("wscript.shell")
ws.run "wsl -d Arch", 0
```

这样当系统启动并登录后，Windows会自动开启WSL实例，它会永久等待输入，不会关闭。下次使用WSL命令时就不会遇到需要重新唤醒WSL的耗时。

如果担心后台挂着WSL对系统资源占用过高，可以通过配置`.wslconfig`文件来限制WSL的资源占用：

```ini
notepad %USERPROFILE%\.wslconfig
[wsl2]
memory=4GB 
processors=2
```

### 15. 解决家目录的问题

```bash
# 输入wsl后不在当前用户的家目录下
cd ~
vim .bashrc
# 最后面添加 cd ~
```

### 16. 解决libcuda.so.1符号链接问题

参考：https://github.com/microsoft/WSL/issues/5548

```bash
# 在/etc/wsl.conf中添加ldconfig=false配置
echo -e "[automount]\nldconfig = false" | sudo tee -a /etc/wsl.conf

# 创建符号链接
sudo mkdir /usr/lib/wsl/lib2
sudo ln -s /usr/lib/wsl/lib/* /usr/lib/wsl/lib2
echo /usr/lib/wsl/lib2 | sudo tee /etc/ld.so.conf.d/ld.wsl.conf
```

### 17. 解决Debian下git log显示乱码

```bash
vim ~/.bashrc
# 添加以下行
export LESSCHARSET=utf-8
```

## 注意事项

- WSL 2要求Windows 10版本为2004及以上或Windows 11
- 导入/导出功能可用于在不同电脑间迁移WSL环境
- 使用systemd可以支持更多需要后台服务的应用
- 合理配置WSL资源限制可以避免影响Windows主系统性能
