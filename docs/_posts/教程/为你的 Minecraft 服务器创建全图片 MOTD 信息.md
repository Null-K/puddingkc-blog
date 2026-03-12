---
title: 为你的 Minecraft 服务器创建全图片 MOTD 信息
categories: 
  - 教程
tags: 
  - 教程
  - Minecraft
  - 服务器
  - 原版
date: 2026-03-12 07:43:01
permalink: /pages/f7a588/
sidebar: auto
author: 
  name: PuddingKC
  link: https://github.com/Null-K
---

## 前言
在 Minecraft Java 版 **1.21.9** 及以上版本中，官方新增了一种名为 [**Object**](https://zh.minecraft.wiki/w/%E6%96%87%E6%9C%AC%E7%BB%84%E4%BB%B6) 的文本组件。<!-- more -->  
通过这种组件，你可以在文本中直接显示 **原版物品图标**，或者 **任意玩家皮肤的头颅**。

既然可以在文本中显示玩家头颅，那么就可以利用 **自定义皮肤** 进行排列与拼接，从而组合成一整张图片。  
换句话说，我们完全可以利用这一特性，在 **不使用任何材质包或模组** 的情况下，为服务器制作一个 **完全由图片组成的 MOTD**。

## 效果

![效果展示](/img/blogs/motd_1.webp)  
![效果展示](/img/blogs/motd_2.webp)

## 教程

1. 首先绘制一张作为 **MOTD 内容** 的图片，推荐分辨率为 **264 × 16** 像素。
2. 将图片按 **8 × 8 像素** 的大小进行切割，并将每个切片制作成 **Minecraft 原版皮肤纹理**。

![裁切展示](/img/blogs/motd_3.webp)

3. 使用 [**MineSkin**](https://mineskin.org/) 或其他类似的 API，将这些皮肤逐个上传并生成对应的皮肤数据。
4. 获取生成后的头颅数据后，使用 **MiniMessage** 或其他文本组件方式，将这些头颅按照正确顺序排列。
5. 配置完成后，即可在服务器 **MOTD** 中显示完整的图片效果。

## 插件
如果觉得以上步骤操作起来比较繁琐，我也制作了一个 **Paper 插件**，可以自动完成这些过程。  
只需要将完整图片放入插件目录中，插件就会自动完成 **图片切割、头颅生成以及 MOTD 排列**，从而快速生成对应的图片 MOTD。

下载地址：[https://www.minebbs.com/resources/headosaic-motd.15638/](https://www.minebbs.com/resources/headosaic-motd.15638/)
