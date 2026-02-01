---
title: 如何下载自己上传到 VRChat 的模型文件
categories: 
  - 教程
tags: 
  - 教程
  - VRChat
date: 2026-02-01 18:19:43
permalink: /pages/25d1d6/
sidebar: auto
author: 
  name: PuddingKC
  link: https://github.com/Null-K
---

## 前言

很多 VRChat 玩家可能都遇到过这样的经历：<!-- more -->
- 模型工程文件搞丢了
- 电脑重装/换机后找不到源文件
- 委托的模型作者已经失联或删档

好消息是：VRChat 官方提供了 [API](https://vrchat.community/reference/download-file-version) 接口可以下载自己账户下模型的未加密 `.vrca` 文件。  
坏消息是：这个功能有一定的上手难度，只能通过调用 API 来实现。

经常有朋友找我说模型工程丢了，想找回文件。  
为了方便大家，我就写了个小工具来下载。

## 重要提醒

该软件仅限下载**自己账号**上传的模型。  
尝试下载他人模型、传播、逆向工程等行为都违反 VRChat 服务条款，严重可能导致账号封禁。

## 使用教程

> 官方仓库：https://github.com/Null-K/VRChatVRCADownloader  
下载地址：https://github.com/Null-K/VRChatVRCADownloader/blob/main/build/vrchat_vrca_downloader.exe

操作步骤：
1. 点击上方下载链接，保存 `vrchat_vrca_downloader.exe` 文件
2. 打开浏览器，进入 [VRChat](https://vrchat.com) 官网，登录你要下载模型的账号
3. 按 **F12** 打开开发者工具，切换到 `应用`（Application）标签

![图片示例](/img/blogs/VRChatVRCADownloader.webp)

4. 在左侧展开 `Cookie` 并选择 `https://vrchat.com`
5. 在右侧列表找到名称为 `auth` 的条目，复制它的 `值`（Value）
6. 打开下载好的下载器，把复制的 auth 值粘贴到软件左上角的输入框
7. 点击 `获取模型` 按钮，等待列表刷新
8. 在列表中双击你想要的模型，即可开始下载

## 其他功能

软件支持下载完成后自动调用 AssetRipper 进行解析，操作方法如下：

1. 下载并安装 [AssetRipper](https://github.com/AssetRipper/AssetRipper/releases)
2. 启动 AssetRipper
3. 在 AssetRipper 控制台窗口中找到类似 `http://127.0.0.1:xxxx` 的后台地址，记下其中的端口号（xxxx部分）
4. 将该端口号填入下载器右上角的输入框中

## 免责声明

本工具为第三方辅助工具，仅用于个人账号资产的管理与下载。  
所有数据请求均通过 VRChat 官方公开 API 接口完成。  
本工具不会修改、伪造或干预任何服务器数据。

本工具：
- 不提供、也不支持任何形式的破解、绕过权限或非法访问行为
- 不包含对 VRChat 客户端、服务器或资源的逆向、注入或篡改
- 不存储、不上传、不分享用户的账号信息或 Cookie