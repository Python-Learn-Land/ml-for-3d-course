# 生成式 3D 流水线

让我们退后一步，从整体上看看生成式 3D 流水线。

![3D 流水线](https://huggingface.co/datasets/huggingface/documentation-images/resolve/main/ml-for-3d-course/3d-pipeline.png)

在第 2 步中，存在某种被标注为「ML 友好的 3D」的[非网格](non-meshes.md)表示，它会通过 Marching Cubes 被转换成网格（第 3 步）。

在「ML 友好的 3D」之前，通常还有一个叫「多视图扩散」（multi-view diffusion）的步骤。这一步使用[扩散](https://huggingface.co/docs/diffusers/en/index)模型（比如 [Stable Diffusion](https://huggingface.co/spaces/stabilityai/stable-diffusion)）来生成物体的新视角 —— 输入可以是源图像，也可以是文本。

流水线的这部分技术性很强且演进极快，与扩散模型的关系比与 3D 的关系更密切。因此在本课程中，我们会把它当作一个基础构件来对待，重点放在如何借助 Hugging Face 生态系统来使用它。

如果你想深入了解扩散模型的具体原理，请查看[扩散课程（Diffusion Course）](https://huggingface.co/learn/diffusion-course/en/unit0/1)。

## 在本课程中

在本课程中，每个核心单元都会围绕这三个基础构件展开：

1. 多视图扩散，重点关注工具与生态
2. ML 友好的 3D，深入剖析高斯泼溅
3. 网格，重点关注实际应用

每个单元还会包含一个动手练习，让你能在真实场景中运用所学内容。
