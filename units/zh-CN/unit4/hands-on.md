# 动手实践

我们最初的计划是完整走一遍 Marching Cubes 算法，并把它应用到 [LGM Demo](https://huggingface.co/spaces/dylanebert/LGM-mini) 上。然而，网格生成领域的最新进展让这种做法变得不再那么有意义。

虽然深入剖析 [MeshAnything](https://huggingface.co/spaces/Yiwen-ntu/MeshAnything) 背后的方法会更有针对性，但考虑到它出现的时间太新，而且采用[非商业许可证](https://github.com/buaacyw/MeshAnything/blob/main/LICENSE.txt)，眼下并不是最合适的选择。

因此，这里根据你的不同目标提供一些资源：

- [Splat to Mesh](https://huggingface.co/spaces/dylanebert/splat-to-mesh)：如果你跟着做完了基于 LGM 的练习，想产出最终的网格，这个开源 Demo 正是基于原始的 [LGM](https://github.com/3DTopia/LGM) 代码库实现的。注意这种方法速度慢且资源消耗大。
- [InstantMesh](https://huggingface.co/spaces/TencentARC/InstantMesh)：这是一种快速且先进的方法，使用 FlexiCubes 来产出最终网格。它目前在 [3D Arena](https://huggingface.co/spaces/dylanebert/3d-arena) 排行榜上位居前列。
- [meshgpt-pytorch](https://github.com/lucidrains/meshgpt-pytorch)：这是 [MeshGPT](https://huggingface.co/papers/2311.15475) 的开源复现版本，是开源可微分网格生成的一个不错起点。[MeshAnything](https://huggingface.co/papers/2311.15475) 就是在 MeshGPT 基础上构建的。注意：这个实现只提供模型架构，不提供权重。

这些资源应该能帮助你继续探索网格生成及其最新进展。
