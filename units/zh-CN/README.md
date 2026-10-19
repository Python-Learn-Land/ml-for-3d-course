# 3D 机器学习课程（中文版）

本目录是 [Hugging Face Machine Learning for 3D Course](https://huggingface.co/learn/ml-for-3d-course/unit0/introduction) 的中文翻译，供本地阅读学习使用。

英文原版位于 [`units/en/`](../en)。

## 目录

### 第 0 单元。课程导览

1. [欢迎 🤗](unit0/introduction.md)
2. [正在发生什么？](unit0/whats-going-on.md)
3. [为什么这很重要？](unit0/why-does-it-matter.md)
4. [如何自己动手](unit0/how-to-do-it-yourself.md)

### 第 1 单元。什么是 3D？

1. [简介](unit1/introduction.md)
2. [网格](unit1/meshes.md)
3. [非网格表示](unit1/non-meshes.md)
4. [生成式 3D 流水线](unit1/pipelines.md)

### 第 2 单元。多视图扩散

1. [简介](unit2/introduction.md)
2. [环境准备](unit2/setup.md)
3. [它是什么？](unit2/what-is-it.md)
4. [流水线](unit2/pipeline.md)
5. [动手实践（第一部分）](unit2/hands-on-1.md)
6. [动手实践（第二部分）](unit2/hands-on-2.md)
7. [拓展练习](unit2/bonus.md)

### 第 3 单元。高斯泼溅

1. [简介](unit3/introduction.md)
2. [它是什么？](unit3/what-is-it.md)
3. [动手实践](unit3/hands-on.md)
4. [拓展练习](unit3/bonus.md)

### 第 4 单元。网格

1. [简介](unit4/introduction.md)
2. [Marching Cubes](unit4/marching-cubes.md)
3. [网格生成](unit4/mesh-generation.md)
4. [动手实践](unit4/hands-on.md)

### 第 5 单元。毕业设计

1. [简介](unit5/introduction.md)
2. [完整演示](unit5/walkthrough.md)
3. [在 Notebook 中运行](unit5/run-in-notebook.md)
4. [本地运行](unit5/run-locally.md)
5. [通过 API 运行](unit5/run-via-api.md)

### 结语

1. [结语](conclusion/conclusion.md)

## 关于本地阅读的调整

相比英文原版，中文版做了以下便于本地阅读的调整：

- 文件格式使用 `.md` 而非 `.mdx`。两者在 doc-builder 中走完全相同的处理路径，构建产物字节级一致。
- 原文中以 `<iframe>` 嵌入的 YouTube 视频，改为可点击的 `watch?v=` 链接（本地 Markdown 阅读器不渲染 iframe）。
- 原文中以 `<iframe>` 嵌入的 Hugging Face Space 交互 Demo，同样改为可点击链接。
- 原文中的 LaTeX 行内公式 `$2^8 = 256$` 改写为 `2⁸ = 256`，以便在不支持数学渲染的阅读器中正常显示。

所有代码块、图片链接和外部链接均与英文原版保持一致，未作改动。

## 术语对照

| 英文 | 中文 |
| --- | --- |
| mesh | 网格 |
| non-mesh | 非网格表示 |
| Gaussian Splatting | 高斯泼溅 |
| splat | 泼溅体 |
| multi-view diffusion | 多视图扩散 |
| differentiable | 可微分（的） |
| rasterization | 光栅化 |
| triplanes | 三平面 |
| Marching Cubes | Marching Cubes（行进立方体） |
| voxel | 体素 |
| low-poly | 低多边形 |
| edge flow | 边流 |
| topology | 拓扑 |
| Janus problem | Janus 问题 |
| capstone | 毕业设计 |
| hands-on | 动手实践 |
| bonus | 拓展练习 |

## 出处

- 原作者：Dylan Ebert（[IndividualKex](https://www.youtube.com/@IndividualKex)）
- 原始仓库：https://github.com/huggingface/ml-for-3d-course
