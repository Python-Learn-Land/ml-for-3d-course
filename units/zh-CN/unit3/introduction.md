# 简介

> 🎬 本节视频（YouTube）：https://www.youtube.com/watch?v=GQXDjzNWuPc

## ML 友好的 3D

让我们退后一步，从整体上考虑生成式 3D 流水线。

![3D 流水线](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/3d-pipeline.png)

多视图扩散之后，接着就是 ML 友好的 3D。这是某种便于 AI 处理的非网格 3D 表示。

在当前的 3D 研究生态中，它可以是很多东西：

- **高斯泼溅（Gaussian Splatting）**：本单元详细讲解
- **三平面（Triplanes）**：当前最先进（state-of-the-art）的方法。可在这份[社区 Notebook](https://colab.research.google.com/github/FeMa42/OpenLRM/blob/main/Introduction_to_triplanes_colab.ipynb) 中了解更多
- **NeRF**：用神经网络合成新视角
- 还有更多

这一切都在飞速变化。所以和多视图扩散的情况一样，ML 友好的 3D 也可以被当作黑盒来处理，直接使用[预训练模型](https://huggingface.co/models?pipeline_tag=image-to-3d&sort=trending)。

## 高斯泼溅

在本单元中，我会深入探讨其中一种：高斯泼溅。

之所以要深入讲它，是因为与其他表示方式不同，泼溅体可以[实时渲染](https://huggingface.co/spaces/dylanebert/4DGS-demo)，这使它们适用于端到端全链路都对 AI 友好的 3D 应用。

让我们开始吧！
