# 拓展练习

本单元深入了解了高斯泼溅 —— 一种可微分光栅化技术，同时也是一种 ML 友好的 3D 表示。虽然凭借实时渲染能力，它算是一个特例，但它只是生成式 3D 研究中众多表示方式之一。

最近，三平面在最终网格质量方面成为新的最先进方法，被用于 [InstantMesh](https://huggingface.co/spaces/TencentARC/InstantMesh) 等流水线中。你可以在 Damian 提供的这份[社区 Notebook](https://colab.research.google.com/github/FeMa42/OpenLRM/blob/main/Introduction_to_triplanes_colab.ipynb) 中了解更多。

如果你想进一步学习高斯泼溅，这里有一些参考资源：

- [Nerfstudio gsplat](https://github.com/nerfstudio-project/gsplat)：高斯泼溅的一个开源实现
- [gsplat.js](https://github.com/huggingface/gsplat.js)：一个用于泼溅体渲染的开源 JavaScript 库
- [UnityGaussianSplatting](https://github.com/aras-p/UnityGaussianSplatting)：高斯泼溅的 Unity 实现（别被它自称的「玩具（toy）」骗了 —— 它其实是个强大的工具！）
- 在本课程的毕业设计中，你可以选择把网格或高斯泼溅作为生成式模型的最终输出

在下一个单元中，我们将深入探讨网格 —— 也就是无处不在的 3D 应用所使用的表示方式。
