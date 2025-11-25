---
title: "git国内使用魔法无法推送"
description: "解决使用代理软件时Git推送失败的问题"
date: 2025-09-15T00:00:00+08:00
categories:
  - 技术分享
tags:
  - Git
  - 代理
  - 网络配置
  - 开发工具
---

## 问题描述

当我们使用魔法软件在Git进行代码推送时，经常会遇到以下类似错误：

```bash
fatal: unable to access 'https://github.com/...': failed to connect to 127.0.0.1 
port 7890 after 2092 ms: couldn't connect to server
```

> **⚠️ 注意：** 这个问题通常是由于代理端口配置不正确导致的。

## 解决方案

### 在开始之前，请确保您的代理服务正常运行，且网络流畅

### 步骤一：查看代理服务器端口

在您的电脑中依次打开：
- 设置 → 网络和Internet → 代理 → 手动设置代理 → 编辑

查看代理服务器地址右侧的端口号，先记下来

### 步骤二：打开cmd终端

依次输入以下命令：

```bash
git config --global http.proxy http://127.0.0.1:(端口号)
git config --global https.proxy http://127.0.0.1:(端口号)
```

### 步骤三：检验配置是否完成

继续在终端输入以下代码：

```bash
git config --global -l
```

> **✅ 成功提示：** 如果看到输出了账户信息，说明配置完成。

## 常见问题

### 如何取消代理配置？

如果以后不需要使用代理，可以通过以下命令取消：

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### 端口号常见值

- **7890** - Clash for Windows 默认端口
- **1080** - 一些代理软件的默认端口
- **8080** - 部分代理工具使用的端口

### 验证代理是否生效

可以通过以下命令测试：

```bash
git ls-remote https://github.com/octocat/Hello-World.git
```

如果能够正常获取远程仓库信息，说明代理配置成功。

## 总结

通过正确配置Git的代理设置，可以解决在使用魔法软件时无法推送代码的问题。关键是要确保：

1. 代理软件正常运行
2. 端口号配置正确
3. 网络连接稳定

希望这篇文章能帮助您解决Git推送的问题！
