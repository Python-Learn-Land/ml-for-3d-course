# 网格生成

目前已经出现了新的解决方案，它们针对 Marching Cubes 的局限性，能够把稠密网格转换为低多边形网格。

在课程前面，我们强调过**可微分性**（differentiability）的重要意义，以及网格渲染为什么是不可微分的 —— 它涉及诸如这样的离散决策：

- 这个像素在三角形内部吗？

虽然这一点仍然成立，但新研究为网格生成引入了一条可微分的路径：它把网格三角形当作离散符号来处理，类似于语言模型中的词。

## MeshAnything

[MeshAnything](https://huggingface.co/spaces/Yiwen-ntu/MeshAnything) 是近期的一项研究工作，它使用 [MeshGPT](https://huggingface.co/papers/2311.15475) 中提出的技术，把稠密网格转换为低多边形网格。

MeshAnything 的主要组成部分有：

1. **VQ-VAE 编码器：** 使用向量量化（Vector Quantization，VQ）变分自编码器（Variational Autoencoder，VAE），把稠密的 3D 数据编码成离散的隐空间表示。
2. **自回归 Transformer 解码器：** 使用自回归 Transformer 解码器来生成网格的各个三角形。

更多细节可见论文 [MeshAnything: Artist-Created Mesh Generation with Autoregressive Transformers](https://huggingface.co/papers/2406.10163)。

![MeshAnything 演示](https://huggingface.co/datasets/dylanebert/ml-for-3d-course/resolve/main/meshanything-demo.gif)

## 意义

MeshAnything 是 3D 网格生成领域的一个重大进步，它解决了迈向实用生成式 3D 工具道路上的一个主要瓶颈。

不过，目前的结果与 [Decimate](https://docs.blender.org/manual/en/latest/modeling/modifiers/generate/decimate.html) 这类传统拓扑简化方法相比，只是持平甚至更差，仍然需要大量人工精修。

可微分网格生成为「上下文感知的拓扑简化」（context-aware topology reduction）打开了大门，也就是能够把形状和形变因素考虑进来。虽然这项工作仍在进行中，但一旦解决，将会催生出非常实用的 3D 工具。
