# 简介

> 🎬 本节视频（YouTube）：https://www.youtube.com/watch?v=IgTYj6tx1Fk

## 网格

让我们重新回顾一下生成式 3D 流水线。

![3D 流水线](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/3d-pipeline.png)

在课程前面，我们讨论过流水线中网格这一侧的局限性，尤其是 Marching Cubes —— 它产出的网格既稠密又粗糙，不适合投入生产。

![Marching Cubes 头部模型](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/Marchingcubes-head.png)

但这已经不是故事的全部了。

近期出现了新的进展，能够以可微分的方式把稠密网格转换为低多边形（low-poly）网格。

![MeshAnything 演示](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/meshanything-demo.gif)

在本单元中，我们将讲解：

- Marching Cubes
- 网格生成

让我们开始吧！
