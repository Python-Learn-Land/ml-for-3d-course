# 它是什么？

高斯泼溅是一种**可微分光栅化技术**（differentiable rasterization technique）。

## 可微分光栅化

简单来说：

- 「可微分」（differentiable）可以理解为「对 AI 友好」的一种花哨说法
- 「光栅化」（rasterization）就是把数据取出来、画到屏幕上

光栅化其实已经非常常见了。它通常表现为[三角形光栅化](https://en.wikipedia.org/wiki/Rasterisation)，即把 3D 数据转换成 2D 像素数据并绘制到屏幕上。网格通常就是这样渲染的。

![网格](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/mesh.png)

然而，三角形光栅化对 AI 并不友好。这是因为它包含了一些离散决策，比如：

- 这个像素在三角形内部吗？

神经网络不喜欢离散决策。它们希望一切都是模糊、连续的 —— 换句话说，就是*可微分*的。

## 高斯泼溅

高斯泼溅是一种可微分光栅化技术。但它具体是怎么工作的？

泼溅体由数百万个点组成，每个点由四个参数构成：

- **位置（Position）**：它在哪里（XYZ）
- **协方差（Covariance）**：它被拉伸成什么样（3x3 矩阵）
- **颜色（Color）**：它是什么颜色（RGB）
- **透明度（Alpha）**：它有多透明（α）

然后，要对一个泼溅体进行光栅化，就把这些点投影到 2D 平面上。接着，对每一个像素，累加每一个点对它的贡献。用伪代码表示：

```python
splat2d = splat.project_and_sort()
for point in splat2d:
    for pixel in image:
        pixel += compute_contribution(point, pixel)
```

一个点对某像素的贡献，会随着它离该像素越远而越小。这些点还需要排序，因为它们是按从后到前的顺序混合（blend）的。

理论上，每个点都会对每个像素产生贡献，这非常低效。但没关系，因为它是*可微分*的。

在实践中，这一点通过基于分块（tile-based）的光栅化方法得到了优化，详见[原始论文](https://huggingface.co/papers/2308.04079)。

## 推理

如果你不是在训练模型，那么它是否可微分就无关紧要了。你可以直接把每个点当作一个实例化四边形（instanced quad）来处理，开源的 Web 查看器（比如 [gsplat.js](https://github.com/huggingface/gsplat.js)）就是这么做的。

你可以在[这里](https://huggingface.co/spaces/dylanebert/igf)看到实际效果。

## 训练

[原始论文](https://huggingface.co/papers/2308.04079)使用[运动恢复结构（Structure-from-Motion）](https://en.wikipedia.org/wiki/Structure_from_motion)来初始化这些点，这是一种传统的 3D 重建算法。

![运动恢复结构](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/124_ml-for-games/gaussian/points.png)

然后使用基于分块的方法对这些点进行光栅化，并通过将光栅化结果与真实图像（ground truth）对比来计算损失。再用梯度下降来调整各个点的参数（位置、协方差、颜色、透明度）。

![训练后](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/124_ml-for-games/gaussian/ellipsoids.png)

原始论文还使用了自动稠密化（densification）和剪枝（pruning），按需自动增删点。更多细节可以在[这里](https://huggingface.co/blog/gaussian-splatting)找到。

![最终结果](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/blog/124_ml-for-games/gaussian/bicycle.png)

## 生成式 3D

原始方法适用于从照片中学习单个场景。不过，可微分光栅化这一概念可以推广到神经网络等更复杂的模型上。

像 [LGM](https://huggingface.co/spaces/dylanebert/LGM-mini) 这样的生成式 3D 模型就是如此。在下一节中，我们将用它来构建自己的生成式 3D Demo。
