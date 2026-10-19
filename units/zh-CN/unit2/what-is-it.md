# 它是什么？

多视图扩散是一种扩散模型（例如 [Stable Diffusion](https://huggingface.co/blog/stable_diffusion)）。不过，它的训练数据不是普通图像，而是同一物体从不同视角拍摄的多个视图。

![多视图扩散](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/multiview.webp)

## 存在的问题

多视图扩散对 3D 很有用。

然而，它并不能开箱即用。它常常会遇到所谓的「Janus 问题」（Janus problem）—— 生成的物体会出现多张脸，更宽泛地说，就是各个视图之间缺乏一致性。

![Janus 问题](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/janus.png)

## 解决方案

像 [MVDream](https://huggingface.co/MVDream/MVDream) 这样最先进的多视图扩散模型，通过专门的技术手段解决了这个问题。

本课程不会深入讲解多视图扩散的技术细节，因为它与扩散模型的关系比与 3D 的关系更密切。但如果你想了解更多，可以查看[扩散课程（Diffusion Course）](https://huggingface.co/learn/diffusion-course/unit0/1)。

在接下来的章节中，我们将使用一个预训练的多视图扩散模型来生成多视图图像。
