# 非网格表示

如今，现实世界应用中几乎所有的 3D 内容都以[网格](meshes.md)表示，但 3D 机器学习研究通常使用非网格（non-mesh）表示，之后再将其转换为网格。

这些非网格表示可能是：

1. 三平面（Triplanes），例如 [InstantMesh](https://huggingface.co/TencentARC/InstantMesh) 中使用的
2. [NeRF](https://en.wikipedia.org/wiki/Neural_radiance_field)（神经辐射场），例如 [NeRFiller](https://huggingface.co/papers/2312.04560) 中使用的
3. [泼溅体](https://en.wikipedia.org/wiki/Gaussian_splatting)（Splats），例如 [LGM](https://huggingface.co/ashawkey/LGM) 中使用的

这些方法在不断演进，甚至可能在你读到本文时就已经发生了变化。

好在，多数情况下你可以把它当成一个黑盒来处理。要在工作中使用这些非网格表示，你并不需要理解它们的内部细节。

不过，其中有一种表示格外值得注意。

> 🕹️ 在线交互 Demo（高斯泼溅编辑器）：https://dylanebert-gsplat-editor.hf.space

## 高斯泼溅

非网格表示中的一个特例是泼溅体（splats），也就是[高斯泼溅](https://en.wikipedia.org/wiki/Gaussian_splatting)（Gaussian Splatting）。

之所以特别，是因为与其他非网格表示不同，泼溅体可以实时渲染。它们还支持[动画](https://huggingface.co/spaces/dylanebert/4DGS-demo)、[物理模拟（混合方式）](https://x.com/HugoDuprez/status/1766019907769000229)和[光照](https://x.com/Ruben_Fro/status/1719996105675698452)等特性。

这意味着从理论上讲，泼溅体可以在现实应用中取代网格。然而，整个现实世界的 3D 生态都是围绕网格构建的，因此泼溅体不太可能取而代之。更可能的情况是，泼溅体与网格在 3D 生态中并存、各自扮演角色，尤其是在实时生成式 3D 这类备受期待的应用中。

## 在本课程中

我们会同时讲解网格和高斯泼溅。

尽管当前的[最先进方法](https://huggingface.co/TencentARC/InstantMesh)使用的是三平面，但我们不会在本课程中深入这些具体细节，因为它们变化太快。

我们会转而聚焦于 3D 机器学习研究的基础构件，然后深入探讨高斯泼溅和网格，因为它们在今天就可以用于现实应用。
