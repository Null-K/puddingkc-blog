---
title: 使用 Nginx 搭建高防流量转发
categories: 
  - 教程
tags: 
  - 教程
  - Nginx
  - 服务器
date: 2026-02-23 07:14:34
permalink: /pages/e59f34/
sidebar: auto
author: 
  name: PuddingKC
  link: https://github.com/Null-K
---

## 前言
最近在帮朋友的服务器接入高防 IP，顺手整理了一下使用到的流量转发方案。  
这种方式主要适用于：<!-- more -->
- 隐藏源站真实 IP
- 利用高防 IP 进行流量清洗
- 对连接做简单的上下行限速

**重要提醒：**`proxy_upload_rate` 和 `proxy_download_rate` 是**单连接限速**，如果玩家多开客户端，限速很容易被绕过。

## 准备工作
- 一台使用 Linux 系统的高防服务器（推荐 Debian 11/12）。
- 源站 IP 可以被高防服务器成功连接。

## 配置流程

### 1. 安裝 Nginx
```bash
sudo apt update  # 更新软件列表
sudo apt install nginx  # 安装 Nginx
```

### 2. 配置 stream 模块
打开 `/etc/nginx/nginx.conf` 文件，并在最下方加入以下內容

```nginx
stream {
    # 限速单位: k = KB/s, m = MB/s
    # 100k ≈ 0.8 Mbps  2m ≈ 16 Mbps

    proxy_upload_rate 100k;  # 玩家 -> 服务器 上行限速
    proxy_download_rate 2m;  # 服务器 -> 玩家 下行限速

    # TCP
    server {
        listen 25565;  # 监听的本机端口
        proxy_pass example.com:25565;  # 源站端口和地址
        proxy_protocol on;  # 开启代理协议，传递玩家真实 IP
        proxy_timeout 300s;
        proxy_connect_timeout 5s;
    }

    # UDP
    server {
        listen 19132 udp;  # 监听的本机端口
        proxy_pass example.com:19132;  # 源站端口和地址
        proxy_timeout 60s;
        proxy_responses 1;  # 可选配置
    }
}
```

### 3. 检查配置并重载
```bash
sudo nginx -t  # 检查配置文件语法正确性
sudo systemctl reload nginx  # 重载 Nginx
```

### 4. 完成配置
配置完成后，用户就可以通过 `<高防地址>:25565` 或 `<高防地址>:19132` 连接到你的后端服务器。  
但记住千万别让源站真实 IP 或域名泄露，否则用户可以直接绕过高防攻击源站，这样保护的目的就失效了。

## 结尾
也没有什么要写的，以上就是完整的配置流程啦。希望对你有帮助。