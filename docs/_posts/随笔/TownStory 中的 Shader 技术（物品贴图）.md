---
sidebar: auto
categories: 
  - 随笔
tags: 
  - 分享
  - 技术
title: TownStory 中的 Shader 技术（物品贴图）
permalink: /pages/townstory-server-shader-item/
date: 2026-09-28 00:39:34
author: 
  name: PuddingKC
  link: https://github.com/Null-K
---

# TownStory 中的 Shader 技术（物品贴图）

在 TownStory 中，有一部分物品并不只是普通的静态贴图。  
数字雨、故障...这些效果实际上都运行在 Minecraft 原版的物品 **Shader** 中。<!-- more -->

![效果展示](/img/blogs/townstory_server_shader_item.gif)

比较有意思的是，我们并没有修改客户端，也没有额外增加一套数据同步机制。对于服务器来说，这些效果仍然只是一个普通的物品模型和资源包。

真正需要解决的问题，反而是 Shader 本身。  
原版的物品渲染并没有给资源包提供一个专门的“效果 ID”。所以我们最后选择利用 `tint`，把它当成一个很轻量的标记。

例如某个特殊物品使用：
```json
"tints": [{ "type": "minecraft:constant", "value": 16711664 }]
```

这个颜色本身并不重要，它更像是一个编号。  
进入 Shader 后，我们会把 tint 重新转换成整数，再根据不同的值选择对应的效果。
```glsl
switch (tintCode) {
    case 0xFEFFF0:
        return FX_MATRIX_RAIN;
    case 0xFEFFF1:
        return FX_RAINBOW;
        
    // ...
}
```

之所以选择接近白色的颜色，是因为即使在没有加载这套 Shader 的环境下，它也不会让物品出现很明显的偏色。

这样一来，`tint` 就同时承担了两个作用：  
一方面，它仍然符合 Minecraft 原本的资源包机制；另一方面，又可以作为我们自己的效果标记。

## 贴图坐标的问题

效果 ID 解决之后，还有一个更麻烦的问题。  
Minecraft 的物品贴图并不是一张贴图对应一个纹理，而是统一放在 **Texture Atlas** 中。

所以 Shader 拿到的 UV 坐标，实际上描述的是：
> “我在整张纹理图集的什么位置。”

而我们真正想知道的是：
> “我在当前这个物品贴图里的什么位置。”

这两者看起来差别不大，但对于一些效果来说非常重要。  
例如数字雨需要知道字符距离贴图顶部还有多远，描边需要判断当前像素周围有没有透明区域。如果一直使用图集坐标，就很难把这些效果做得稳定。

最后采用的办法有点“曲线救国”。

我们在模型的四个顶点上额外记录一个简单的四边形坐标，然后利用 Shader 提供的屏幕空间导数，把它和实际的纹理坐标对应起来。  
简单来说，就是根据一个四边形在屏幕上的变化情况，反推出它在纹理图集里的范围。

核心计算大致是：
```glsl
mat2 C = mat2(
    dFdx(quadCoord),
    dFdy(quadCoord)
);

mat2 M = mat2(dFdx(t), dFdy(t)) * inverse(C);

vec2 p00 = t - M * quadCoord;
```

这样处理之后，我们就可以重新得到当前物品贴图的原点、尺寸，以及当前像素在贴图内部的位置。  
有了这些信息，后面的事情就简单很多了。

## 从坐标到效果

我们在 Shader 里把这部分能力封装成了几个比较基础的函数。  
比如获取贴图内部坐标、读取贴图中的其他像素，以及判断当前像素是不是轮廓。

于是具体的效果就不需要再关心 Texture Atlas 是怎么工作的。  
数字雨只需要处理自己的动画位置。  
描边只需要检查周围的像素。

它们实际上都是建立在同一套基础坐标之上的。

所以从代码结构上看，Shader 并不是一大段针对每个效果分别处理的逻辑，而更像是：
```
基础坐标 > 纹理采样 > 通用工具 > 具体效果
```

## 性能

这套方案对普通物品的影响比较有限。  
即使没有使用任何特殊效果，也只需要进行一些坐标计算和一次效果匹配。

真正比较复杂的 Shader 运算只会出现在命中特效的物品上，而物品本身通常不会占据太大的屏幕面积。

当然，它也不是完全没有限制。  
目前的实现依赖原版物品模型的四边形顶点结构，因此一些特殊模型可能无法正常工作。

### 参考

1. OpenGL Wiki — GLSL: [https://wikis.khronos.org/opengl/GLSL](https://wikis.khronos.org/opengl/GLSL)
2. OpenGL Wiki — Core Language (GLSL): [https://wikis.khronos.org/opengl/Core_Language_%28GLSL%29](https://wikis.khronos.org/opengl/Core_Language_%28GLSL%29)
3. OpenGL Wiki — Interpolation: [https://wikis.khronos.org/opengl/Interpolation](https://wikis.khronos.org/opengl/Interpolation)
4. OpenGL 4.60 Shading Language Specification: [https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html](https://registry.khronos.org/OpenGL/specs/gl/GLSLangSpec.4.60.html)
