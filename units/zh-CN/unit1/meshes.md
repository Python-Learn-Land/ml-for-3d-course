# 网格

## 什么是网格？

[网格](https://en.wikipedia.org/wiki/Polygon_mesh)（mesh）是由顶点、边和面组成的集合，用来定义一个 3D 物体。

如今，现实世界应用中几乎所有的 3D 内容都是以这种方式表示的。

![网格](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/mesh.png)

## 问题所在

网格对机器学习模型来说很难处理。

如今的 3D 研究大多是这样的流程：

1. 机器学习流水线先产出一种非网格的 3D 表示
2. 再使用 [Marching Cubes](https://en.wikipedia.org/wiki/Marching_cubes) 把这种非网格表示转换成网格

第 1 步的进展非常迅猛，但第 2 步自 1980 年代以来几乎没有变化。这就在 3D 研究（其突破都发生在第 1 步）和 3D 应用（依赖的是第 2 步的最终输出）之间造成了断层。

这是一种简化说法，但它揭示出 3D 机器学习领域整体面临的挑战。

## 出路

第 2 步究竟会被改进，还是整个 3D 生态会发生彻底改变（抑或介于两者之间），仍有待观察。

在此期间，理解当前 3D 机器学习研究的各种基础构件是很有用的。让我们从[非网格表示](non-meshes.md)开始。
